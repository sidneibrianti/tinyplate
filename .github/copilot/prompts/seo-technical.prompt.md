---
agent: 'agent'
description: Implement technical SEO infrastructure for Next.js apps. Use this prompt when setting up sitemaps, robots.txt, meta tags, OpenGraph, structured data (JSON-LD), canonical URLs, and other technical SEO elements. Covers Next.js 15/16 App Router patterns and 2025 best practices.
tools: ["editFiles", "codebase", "terminal"]
---

# Technical SEO Implementation (Next.js 2025)

## What This Prompt Covers

1. **Sitemaps** → `app/sitemap.ts` for dynamic sitemap generation
2. **Robots.txt** → `app/robots.ts` for crawler directives
3. **Meta Tags** → OpenGraph, Twitter Cards, keywords, descriptions
4. **Structured Data** → JSON-LD for rich snippets
5. **Canonical URLs** → Prevent duplicate content issues
6. **Performance SEO** → Core Web Vitals considerations

---

# Part 1: Sitemap Implementation

## Next.js App Router Sitemap (app/sitemap.ts)

Next.js automatically serves `/sitemap.xml` when you create `app/sitemap.ts`:

```typescript
import type { MetadataRoute } from "next";

const BASE_URL = process.env.NEXT_PUBLIC_SITE_URL || "https://example.com";

export default function sitemap(): MetadataRoute.Sitemap {
  const currentDate = new Date().toISOString();

  // Static pages
  const staticPages: MetadataRoute.Sitemap = [
    {
      url: BASE_URL,
      lastModified: currentDate,
      changeFrequency: "weekly",
      priority: 1.0,
    },
    {
      url: `${BASE_URL}/pricing`,
      lastModified: currentDate,
      changeFrequency: "monthly",
      priority: 0.8,
    },
    {
      url: `${BASE_URL}/about`,
      lastModified: currentDate,
      changeFrequency: "monthly",
      priority: 0.7,
    },
    {
      url: `${BASE_URL}/privacy`,
      lastModified: currentDate,
      changeFrequency: "yearly",
      priority: 0.3,
    },
    {
      url: `${BASE_URL}/terms`,
      lastModified: currentDate,
      changeFrequency: "yearly",
      priority: 0.3,
    },
  ];

  return staticPages;
}
```

### Dynamic Sitemap with Database Content

```typescript
import type { MetadataRoute } from "next";
import { db } from "@/lib/db";
import { blogPosts, products } from "@/lib/db/schema";

const BASE_URL = process.env.NEXT_PUBLIC_SITE_URL || "https://example.com";

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  // Fetch dynamic content
  const posts = await db.select().from(blogPosts).where(eq(blogPosts.published, true));
  const allProducts = await db.select().from(products);

  const staticPages: MetadataRoute.Sitemap = [
    { url: BASE_URL, lastModified: new Date(), changeFrequency: "weekly", priority: 1.0 },
  ];

  const blogPages: MetadataRoute.Sitemap = posts.map((post) => ({
    url: `${BASE_URL}/blog/${post.slug}`,
    lastModified: post.updatedAt || post.createdAt,
    changeFrequency: "weekly" as const,
    priority: 0.7,
  }));

  const productPages: MetadataRoute.Sitemap = allProducts.map((product) => ({
    url: `${BASE_URL}/products/${product.slug}`,
    lastModified: product.updatedAt,
    changeFrequency: "daily" as const,
    priority: 0.8,
  }));

  return [...staticPages, ...blogPages, ...productPages];
}
```

---

# Part 2: Robots.txt Implementation

## Next.js App Router Robots (app/robots.ts)

```typescript
import type { MetadataRoute } from "next";

const BASE_URL = process.env.NEXT_PUBLIC_SITE_URL || "https://example.com";

export default function robots(): MetadataRoute.Robots {
  const isProduction = process.env.NODE_ENV === "production";

  // Block everything in non-production
  if (!isProduction) {
    return {
      rules: { userAgent: "*", disallow: "/" },
    };
  }

  return {
    rules: [
      {
        userAgent: "*",
        allow: "/",
        disallow: [
          "/api/",
          "/dashboard/",
          "/admin/",
          "/private/",
          "/_next/",
          "/sign-in/",
          "/sign-up/",
        ],
      },
      // Block AI training bots (optional)
      { userAgent: "GPTBot", disallow: "/" },
      { userAgent: "ChatGPT-User", disallow: "/" },
      { userAgent: "CCBot", disallow: "/" },
      { userAgent: "anthropic-ai", disallow: "/" },
      { userAgent: "Google-Extended", disallow: "/" },
    ],
    sitemap: `${BASE_URL}/sitemap.xml`,
  };
}
```

---

# Part 3: Metadata Implementation

## Root Layout Metadata (app/layout.tsx)

```typescript
import type { Metadata, Viewport } from "next";

const BASE_URL = process.env.NEXT_PUBLIC_SITE_URL || "https://example.com";

export const viewport: Viewport = {
  width: "device-width",
  initialScale: 1,
  themeColor: "#6366f1",
};

export const metadata: Metadata = {
  metadataBase: new URL(BASE_URL),

  // Title template for child pages
  title: {
    default: "Brand Name — Tagline",
    template: "%s | Brand Name",
  },

  // Description (150-160 chars ideal)
  description: "Your compelling meta description that includes primary keywords and encourages clicks.",

  // Keywords (less important now, but include)
  keywords: ["primary keyword", "secondary keyword", "brand name"],

  // Author info
  authors: [{ name: "Brand Name" }],
  creator: "Brand Name",
  publisher: "Brand Name",

  // Robots directives
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      "max-video-preview": -1,
      "max-image-preview": "large",
      "max-snippet": -1,
    },
  },

  // OpenGraph (Facebook, LinkedIn, etc.)
  openGraph: {
    type: "website",
    locale: "en_US",
    url: BASE_URL,
    siteName: "Brand Name",
    title: "Brand Name — Tagline",
    description: "Your compelling description for social sharing.",
    images: [
      {
        url: "/og-image.png",
        width: 1200,
        height: 630,
        alt: "Brand Name - Description",
      },
    ],
  },

  // Twitter Card
  twitter: {
    card: "summary_large_image",
    title: "Brand Name — Tagline",
    description: "Your compelling description for Twitter.",
    images: ["/og-image.png"],
    creator: "@username",
  },

  // Canonical URL
  alternates: {
    canonical: BASE_URL,
  },
};
```

---

# Part 4: Structured Data (JSON-LD)

## Organization Schema (Homepage)

```typescript
const organizationSchema = {
  "@context": "https://schema.org",
  "@type": "Organization",
  name: "Brand Name",
  url: "https://example.com",
  logo: "https://example.com/logo.png",
  sameAs: [
    "https://twitter.com/brand",
    "https://linkedin.com/company/brand",
  ],
};
```

## Article Schema (Blog Posts)

```typescript
const articleSchema = {
  "@context": "https://schema.org",
  "@type": "Article",
  headline: "Article Title",
  description: "Article description...",
  image: ["https://example.com/blog/article/cover.jpg"],
  datePublished: "2025-01-15T08:00:00+00:00",
  dateModified: "2025-01-20T10:30:00+00:00",
  author: {
    "@type": "Person",
    name: "Author Name",
  },
  publisher: {
    "@type": "Organization",
    name: "Brand Name",
    logo: {
      "@type": "ImageObject",
      url: "https://example.com/logo.png",
    },
  },
};
```

## FAQ Schema

```typescript
const faqSchema = {
  "@context": "https://schema.org",
  "@type": "FAQPage",
  mainEntity: [
    {
      "@type": "Question",
      name: "Question 1?",
      acceptedAnswer: {
        "@type": "Answer",
        text: "Answer 1.",
      },
    },
  ],
};
```

---

# Technical SEO Checklist

## Before Development

- [ ] Define `NEXT_PUBLIC_SITE_URL` for all environments
- [ ] Decide on www vs non-www (pick one, redirect the other)
- [ ] Decide on trailing slashes (consistent throughout)
- [ ] Plan URL structure for all content types

## Core Files

- [ ] File exists at `app/sitemap.ts`
- [ ] File exists at `app/robots.ts`
- [ ] `metadataBase` set to production URL
- [ ] OpenGraph configured with all required fields
- [ ] OG image exists at `public/og-image.png` (1200x630)

## Post-Launch

- [ ] Property verified in Google Search Console
- [ ] Sitemap submitted
- [ ] Test with Google Rich Results Test
- [ ] Test with Facebook Sharing Debugger
- [ ] Mobile-friendly test passed
- [ ] PageSpeed Insights score > 90
