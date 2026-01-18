---
agent: 'agent'
description: Implement PostHog analytics, feature flags, and session replay for Next.js apps. Use this prompt for event tracking, user identification, A/B testing, experiments, and session recording setup. Also handles analytics reporting (funnel analysis, retention, SEO) with Google Search Console integration.
tools: ["editFiles", "codebase", "terminal", "fetch"]
---

# PostHog Implementation (Next.js 2025)

## What This Prompt Covers

1. **Analytics** - Event tracking, user identification, group analytics
2. **Feature Flags** - Boolean flags, multivariate, A/B testing
3. **Session Replay** - Recording setup, privacy controls
4. **Analytics Queries** - HogQL, Query API, extracting insights
5. **Reporting** - Funnel analysis, retention, error reports, SEO

## Quick Setup

### Environment Variables

```bash
# .env.local
NEXT_PUBLIC_POSTHOG_KEY=phc_your_project_key
NEXT_PUBLIC_POSTHOG_HOST=https://us.i.posthog.com
```

### Reverse Proxy Setup (RECOMMENDED)

**IMPORTANT:** Ad blockers block direct PostHog requests. Use a reverse proxy to route through your own domain.

Add to `next.config.ts`:

```typescript
const nextConfig: NextConfig = {
  async rewrites() {
    return [
      {
        source: "/ingest/static/:path*",
        destination: "https://us-assets.i.posthog.com/static/:path*",
      },
      {
        source: "/ingest/:path*",
        destination: "https://us.i.posthog.com/:path*",
      },
      {
        source: "/ingest/decide",
        destination: "https://us.i.posthog.com/decide",
      },
    ];
  },
  // ... rest of config
};
```

Also update CSP headers to allow PostHog connections:
```typescript
"connect-src 'self' ... https://*.posthog.com https://us.i.posthog.com https://us-assets.i.posthog.com",
```

### PostHog Provider (Client-Side)

Create `app/providers.tsx`:

```typescript
'use client'

import posthog from 'posthog-js'
import { PostHogProvider as PHProvider } from 'posthog-js/react'
import { useEffect } from 'react'

export function PostHogProvider({ children }: { children: React.ReactNode }) {
  useEffect(() => {
    posthog.init(process.env.NEXT_PUBLIC_POSTHOG_KEY!, {
      // Use reverse proxy to bypass ad blockers
      api_host: '/ingest',
      ui_host: 'https://us.i.posthog.com',
      defaults: '2025-05-24',
      capture_pageview: false, // We handle manually for accurate funnels
      person_profiles: 'identified_only',
    })
  }, [])

  return <PHProvider client={posthog}>{children}</PHProvider>
}
```

### PostHog Server Client

Create `lib/posthog-server.ts`:

```typescript
import { PostHog } from 'posthog-node'

let posthogClient: PostHog | null = null

export function getPostHogServer(): PostHog {
  if (!posthogClient) {
    posthogClient = new PostHog(process.env.NEXT_PUBLIC_POSTHOG_KEY!, {
      host: process.env.NEXT_PUBLIC_POSTHOG_HOST || 'https://us.i.posthog.com',
      flushAt: 1,
      flushInterval: 0,
    })
  }
  return posthogClient
}
```

## Event Naming Convention

| Pattern | Example | Use Case |
|---------|---------|----------|
| `category:object_action` | `signup:form_submit` | User actions |
| `feature:action` | `dashboard:project_create` | Feature usage |
| `lifecycle:event` | `user:signup_complete` | User journey |

### Property Naming

| Pattern | Example | Type |
|---------|---------|------|
| `object_adjective` | `user_id`, `item_price` | Any |
| `is_` prefix | `is_subscribed`, `is_first_time` | Boolean |
| `has_` prefix | `has_seen_onboarding` | Boolean |
| `_count` suffix | `item_count`, `generation_count` | Number |
| `_at` suffix | `created_at`, `upgraded_at` | Timestamp |

## Server vs Client Decision Tree

```
Where to track?
├── User action in browser → Client (posthog-js)
├── API route / webhook → Server (posthog-node)
├── Server Component render → Server (posthog-node)
├── Need 100% accuracy → Server (no ad blockers)
└── Real-time UI feedback → Client (posthog-js)
```

## Common Pitfalls

| Pitfall | Solution |
|---------|----------|
| **Ad blockers blocking PostHog** | Use reverse proxy (`/ingest` → PostHog). See setup above |
| Events not appearing | Check ad blockers, verify API key, use reverse proxy |
| Duplicate pageviews | Use `capture_pageview: false` and handle manually |
| Feature flag flicker | Bootstrap flags via middleware |
| Missing user data | Call `identify()` BEFORE `$pageview` for accurate funnels |
| Inconsistent naming | Use `category:object_action` pattern |
| `Failed to fetch` errors | Browser extension blocking - use reverse proxy |
| 503 from `us-assets.i.posthog.com` | Ad blocker injecting fake response - use reverse proxy |

## Clarifying Questions

Before implementing PostHog, ask:

1. **What events are most important to track?** (signups, conversions, feature usage)
2. **Do you need server-side tracking?** (for accuracy, API routes)
3. **Are you running A/B tests?** (need experiment setup)
4. **What's your auth provider?** (for user identification pattern)
5. **Do you need session replay?** (privacy considerations)

## Feature Flags

### Boolean Flags

```typescript
'use client'
import { useFeatureFlagEnabled } from 'posthog-js/react'

function Dashboard() {
  const showNewDashboard = useFeatureFlagEnabled('new-dashboard')

  if (showNewDashboard === undefined) {
    return <DashboardSkeleton />
  }

  return showNewDashboard ? <NewDashboard /> : <OldDashboard />
}
```

### Server-Side Feature Flags

```typescript
import { getPostHogServer } from '@/lib/posthog-server'
import { auth } from '@clerk/nextjs/server'

export async function POST(request: NextRequest) {
  const { userId } = await auth()
  const posthog = getPostHogServer()

  const useNewModel = await posthog.isFeatureEnabled('new-model', userId!)

  await posthog._shutdown()
  // ... rest of logic
}
```

## User Identification

### With Clerk

```typescript
'use client'

import { useEffect } from 'react'
import { useAuth, useUser } from '@clerk/nextjs'
import { usePostHog } from 'posthog-js/react'

export function PostHogIdentify() {
  const { isSignedIn, userId } = useAuth()
  const { user } = useUser()
  const posthog = usePostHog()

  useEffect(() => {
    if (isSignedIn && userId && user) {
      posthog.identify(userId, {
        email: user.primaryEmailAddress?.emailAddress,
        name: user.fullName,
        created_at: user.createdAt?.toISOString(),
      })
    } else if (!isSignedIn) {
      posthog.reset()
    }
  }, [isSignedIn, userId, user, posthog])

  return null
}
```
