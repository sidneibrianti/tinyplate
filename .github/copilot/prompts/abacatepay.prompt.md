---
agent: 'agent'
description: "Help with AbacatePay payment integration in Next.js projects. Use when implementing PIX payments, managing subscriptions, handling webhooks, or debugging payment flows. Covers SDK usage, webhook verification, and billing management for Brazilian SaaS applications."
tools: ["editFiles", "codebase", "terminal"]
---

# AbacatePay Integration Helper

Assist with AbacatePay payment gateway integration for Brazilian SaaS applications.

## Quick Reference

### Installation
```bash
bun add abacatepay-nodejs-sdk
```

### Environment Variables
```bash
ABACATEPAY_API_KEY="abp_live_..."      # API key from dashboard
ABACATEPAY_WEBHOOK_SECRET="whsec_..."  # Webhook secret
NEXT_PUBLIC_APP_URL="https://..."      # For callback URLs
```

### SDK Initialization
```typescript
import AbacatePay from "abacatepay-nodejs-sdk";
const abacate = AbacatePay(process.env.ABACATEPAY_API_KEY!);
```

## Common Tasks

### 1. Create a PIX Payment

```typescript
const response = await abacate.billing.create({
  frequency: "ONE_TIME",
  methods: ["PIX"],
  products: [{
    externalId: "plan-pro",
    name: "Plano Pro",
    quantity: 1,
    price: 2990, // R$ 29,90 in centavos
  }],
  customer: {
    email: "user@example.com",
    name: "João Silva",
  },
  returnUrl: "https://app.com/pricing",
  completionUrl: "https://app.com/billing/success",
});

// response.data: { id, url, status, amount }
```

### 2. Create PIX QR Code (Direct)

```typescript
const response = await abacate.pixQrCode.create({
  amount: 2990, // R$ 29,90
  expiresIn: 3600, // 1 hour
  description: "Payment description",
});

// response.data: { id, brCode, brCodeBase64, status, expiresAt }
```

### 3. Check Payment Status

```typescript
const response = await abacate.pixQrCode.check({ id: "pix_abc123" });
// response.data.status: "PENDING" | "PAID" | "EXPIRED" | "CANCELLED"
```

### 4. Simulate Payment (Dev Mode)

```typescript
await abacate.pixQrCode.simulatePayment({ id: "pix_abc123" });
```

## Webhook Handling

### Signature Verification (HMAC-SHA256)

```typescript
import crypto from "crypto";

function validateSignature(payload: string, signature: string, secret: string): boolean {
  const expected = crypto
    .createHmac("sha256", secret)
    .update(payload)
    .digest("hex");
  return crypto.timingSafeEqual(Buffer.from(signature), Buffer.from(expected));
}
```

### Webhook Events

| Event | Description |
|-------|-------------|
| `billing.paid` | Payment confirmed via PIX |
| `withdraw.done` | Withdrawal completed |
| `withdraw.failed` | Withdrawal failed |

### Webhook Payload Structure

```typescript
interface WebhookPayload {
  id: string;              // Event ID (use for idempotency)
  event: string;           // Event type
  devMode: boolean;        // True if test environment
  data: {
    billing?: {
      id: string;
      amount: number;
      status: string;
    };
  };
}
```

### Webhook Handler

```typescript
// app/api/webhooks/abacatepay/route.ts
import { NextRequest, NextResponse } from "next/server";
import crypto from "crypto";

export async function POST(request: NextRequest) {
  const body = await request.text();
  const signature = request.headers.get("x-abacate-signature")!;

  // Verify signature
  const expected = crypto
    .createHmac("sha256", process.env.ABACATEPAY_WEBHOOK_SECRET!)
    .update(body)
    .digest("hex");

  if (!crypto.timingSafeEqual(Buffer.from(signature), Buffer.from(expected))) {
    return NextResponse.json({ error: "Invalid signature" }, { status: 401 });
  }

  const payload = JSON.parse(body);

  // Idempotency check
  const [existing] = await db
    .select()
    .from(webhookEvents)
    .where(eq(webhookEvents.id, payload.id))
    .limit(1);

  if (existing) {
    return NextResponse.json({ message: "Already processed" });
  }

  // Store event before processing
  await db.insert(webhookEvents).values({
    id: payload.id,
    eventType: payload.event,
    payload: payload,
  });

  // Handle event
  switch (payload.event) {
    case "billing.paid":
      await handlePaymentSuccess(payload.data.billing);
      break;
  }

  return NextResponse.json({ received: true });
}
```

## Pricing

| Method | Fee |
|--------|-----|
| PIX | R$ 0,80 flat per transaction |
| Credit Card | 3.5% + R$ 0,60 |
| Withdrawal | R$ 0,80 (up to 20/month) |

## Status Values

| Status | Description |
|--------|-------------|
| `PENDING` | Awaiting payment |
| `PAID` | Payment confirmed |
| `EXPIRED` | Payment window closed |
| `CANCELLED` | Manually cancelled |

## Feature Gating

```typescript
async function checkFeatureAccess(userId: string): Promise<boolean> {
  const [sub] = await db
    .select()
    .from(subscriptions)
    .where(eq(subscriptions.userId, userId))
    .limit(1);

  if (!sub) return false;

  const now = new Date();
  return sub.status === "active" && sub.currentPeriodEnd > now;
}
```

## Price Formatting

```typescript
export function formatPriceBRL(centavos: number): string {
  const reais = centavos / 100;
  return reais.toLocaleString("pt-BR", {
    style: "currency",
    currency: "BRL",
  });
}

// Usage
formatPriceBRL(2990);  // "R$ 29,90"
```

## Testing Checklist

- [ ] Environment variables configured
- [ ] SDK connects successfully
- [ ] Checkout creates billing and returns URL
- [ ] Webhook receives events (use AbacatePay dashboard)
- [ ] Payment status updates correctly
- [ ] Subscription created after payment
- [ ] Idempotency prevents duplicates

## Local Development with ngrok

```bash
# Terminal 1: Run your app
bun run dev

# Terminal 2: Expose with ngrok
ngrok http 3000

# Use ngrok URL in AbacatePay dashboard for webhooks
```
