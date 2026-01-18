---
agent: 'agent'
description: "Setup domains in Cloudflare with DNS for Clerk, Vercel, and email routing. Use when adding new domains, configuring DNS records, or setting up email redirects."
tools: ["editFiles", "terminal", "fetch"]
---

# Cloudflare Setup

Automate Cloudflare workflows: DNS setup, Clerk integration, Vercel deployment, email routing, and R2 storage.

## Prerequisites

### Authentication (Choose One)

**Option 1: API Token (Recommended)**
```bash
# Add to .env.local
CLOUDFLARE_API_TOKEN="your-api-token"
CLOUDFLARE_ACCOUNT_ID="your-account-id"
```

Create token at: https://dash.cloudflare.com/profile/api-tokens
Required permissions:
- Zone:DNS:Edit
- Zone:Zone:Read
- Email Routing Addresses:Edit
- Email Routing Rules:Edit
- Account:R2:Edit (for R2 storage)

**Option 2: Wrangler CLI**
```bash
# Install wrangler
bun add -g wrangler

# Login (opens browser)
wrangler login

# Verify
wrangler whoami
```

### Other Tools
```bash
# Vercel CLI (required)
bun add -g vercel
vercel login
```

## Workflow

When setting up a new domain, follow these steps:

### Step 1: Gather Information

Ask the user for:
1. **Domain name** (e.g., `example.com`)
2. **Clerk DNS records** (paste from Clerk dashboard)
3. **Vercel project name** (e.g., `my-app`)
4. **Email addresses** to create (e.g., `contact`, `support`)
5. **Redirect target email** (e.g., `me@gmail.com`)

### Step 2: Get Zone ID

```bash
# If using API token
curl -X GET "https://api.cloudflare.com/client/v4/zones?name=DOMAIN" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" | jq '.result[0].id'
```

### Step 3: Create DNS Records for Clerk

```bash
# CNAME record for Clerk
curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONE_ID/dns_records" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "type": "CNAME",
    "name": "clerk",
    "content": "frontend-api.clerk.dev",
    "ttl": 1,
    "proxied": false
  }'
```

### Step 4: Add Domain to Vercel

```bash
# Add domain to Vercel project
vercel domains add DOMAIN --scope=TEAM_SLUG
```

Then create Vercel DNS records:

```bash
# A record for root domain
curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONE_ID/dns_records" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "type": "A",
    "name": "@",
    "content": "76.76.21.21",
    "ttl": 1,
    "proxied": false
  }'

# CNAME for www subdomain
curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONE_ID/dns_records" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "type": "CNAME",
    "name": "www",
    "content": "cname.vercel-dns.com",
    "ttl": 1,
    "proxied": false
  }'
```

### Step 5: Setup Email Routing

```bash
# Add destination email (must be verified)
curl -X POST "https://api.cloudflare.com/client/v4/accounts/ACCOUNT_ID/email/routing/addresses" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "email": "your-main-email@gmail.com"
  }'

# Create routing rule for contact@domain.com
curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONE_ID/email/routing/rules" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "name": "Forward contact",
    "enabled": true,
    "matchers": [{"type": "literal", "field": "to", "value": "contact@DOMAIN"}],
    "actions": [{"type": "forward", "value": ["your-main-email@gmail.com"]}]
  }'
```

Required MX records for email routing:
```bash
# MX Records
curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONE_ID/dns_records" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{"type": "MX", "name": "@", "content": "route1.mx.cloudflare.net", "priority": 69, "ttl": 1}'

curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONE_ID/dns_records" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{"type": "MX", "name": "@", "content": "route2.mx.cloudflare.net", "priority": 46, "ttl": 1}'

curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONE_ID/dns_records" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{"type": "MX", "name": "@", "content": "route3.mx.cloudflare.net", "priority": 89, "ttl": 1}'

# SPF Record
curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONE_ID/dns_records" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{"type": "TXT", "name": "@", "content": "v=spf1 include:_spf.mx.cloudflare.net ~all", "ttl": 1}'
```

---

# R2 Storage Setup

## Create Bucket

```bash
# Using wrangler
wrangler r2 bucket create my-bucket

# List buckets
wrangler r2 bucket list
```

## CORS Configuration

```bash
# Create cors.json
cat > cors.json << 'EOF'
{
  "corsRules": [
    {
      "allowedOrigins": ["https://myapp.com", "http://localhost:3000"],
      "allowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
      "allowedHeaders": ["*"],
      "exposeHeaders": ["ETag", "Content-Length"],
      "maxAgeSeconds": 3600
    }
  ]
}
EOF

# Apply CORS
wrangler r2 bucket cors put my-bucket --file=cors.json
```

## Using with AWS SDK (Node.js)

```typescript
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";

const r2 = new S3Client({
  region: "auto",
  endpoint: process.env.R2_ENDPOINT,
  credentials: {
    accessKeyId: process.env.R2_ACCESS_KEY_ID!,
    secretAccessKey: process.env.R2_SECRET_ACCESS_KEY!,
  },
});

// Upload file
await r2.send(new PutObjectCommand({
  Bucket: "my-bucket",
  Key: "uploads/image.png",
  Body: fileBuffer,
  ContentType: "image/png",
}));
```

## R2 Pricing

- Storage: $0.015/GB per month
- Class A operations (PUT, POST, LIST): $4.50 per million
- Class B operations (GET, HEAD): $0.36 per million
- **Egress: FREE** (no bandwidth charges)

## Common DNS Record Types

| Type | Use Case | Proxied |
|------|----------|---------|
| A | Root domain to IP | No (for Vercel) |
| CNAME | Subdomain to hostname | No (for Clerk/Vercel) |
| TXT | Verification, SPF | N/A |
| MX | Email routing | N/A |

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Zone not found | Domain must be added to Cloudflare first |
| DNS propagation slow | Wait 5-10 minutes, check with `dig` |
| Email not forwarding | Verify destination email first |
| Vercel 404 | Check DNS proxied=false for Vercel records |
| Clerk verification failed | Ensure TXT record is on root (@) |

## Useful Commands

```bash
# Check DNS propagation
dig DOMAIN +short
dig DOMAIN MX +short
dig DOMAIN TXT +short

# List zones in account
curl -X GET "https://api.cloudflare.com/client/v4/zones" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" | jq '.result[] | {name, id}'
```
