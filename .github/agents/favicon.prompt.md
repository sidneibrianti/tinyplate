---
agent: 'agent'
description: "Generate favicons and app icons for Next.js projects. Creates all required sizes, formats, and configures metadata."
tools: ["editFiles", "codebase", "terminal", "fetch"]
---

# Favicon Generator

Generate complete favicon sets for Next.js projects.

## Workflow

### Step 1: Auto-Detect App Information

**IMPORTANT:** Before asking the user anything, scan the codebase to extract:

1. **Package.json** - App name and description
2. **Next.js Metadata (app/layout.tsx)** - Title and description
3. **README.md** - App name from H1
4. **Tailwind Config** - Brand colors
5. **CSS Variables** - Primary colors
6. **Existing Icons** - Check public/ and app/

### Step 2: Present Findings & Confirm

After scanning, present what was found:

```
I found the following from your codebase:

App name: MyApp
Description: A study platform
Brand color: #8b5cf6 (from Tailwind config)
Existing icons: None found

Should I generate a favicon based on this? Or would you like to customize?
```

### Step 3: Choose Generation Method

---

## Option A: Generate from Description (No Source Image)

### Text/Initial-Based Icon

Best for: Professional SaaS apps, clean minimal branding.

```typescript
// app/icon.tsx
import { ImageResponse } from "next/og";

export const runtime = "edge";
export const contentType = "image/png";
export const size = { width: 32, height: 32 };

export default function Icon() {
  return new ImageResponse(
    (
      <div
        style={{
          background: "linear-gradient(135deg, #6366f1 0%, #8b5cf6 100%)",
          width: "100%",
          height: "100%",
          display: "flex",
          alignItems: "center",
          justifyContent: "center",
          borderRadius: 8,
          color: "white",
          fontSize: 20,
          fontWeight: 700,
          fontFamily: "system-ui, sans-serif",
        }}
      >
        S
      </div>
    ),
    { ...size }
  );
}
```

### Apple Touch Icon

```typescript
// app/apple-icon.tsx
import { ImageResponse } from "next/og";

export const runtime = "edge";
export const contentType = "image/png";
export const size = { width: 180, height: 180 };

export default function AppleIcon() {
  return new ImageResponse(
    (
      <div
        style={{
          background: "linear-gradient(135deg, #6366f1 0%, #8b5cf6 100%)",
          width: "100%",
          height: "100%",
          display: "flex",
          alignItems: "center",
          justifyContent: "center",
          borderRadius: 40,
          color: "white",
          fontSize: 100,
          fontWeight: 700,
          fontFamily: "system-ui, sans-serif",
        }}
      >
        S
      </div>
    ),
    { ...size }
  );
}
```

### Emoji-Based Icon

Best for: Fun apps, MVPs, quick prototypes.

```typescript
// app/icon.tsx
import { ImageResponse } from "next/og";

export const runtime = "edge";
export const contentType = "image/png";
export const size = { width: 32, height: 32 };

export default function Icon() {
  return new ImageResponse(
    (
      <div
        style={{
          background: "#f8fafc",
          width: "100%",
          height: "100%",
          display: "flex",
          alignItems: "center",
          justifyContent: "center",
          borderRadius: 6,
          fontSize: 24,
        }}
      >
        🚀
      </div>
    ),
    { ...size }
  );
}
```

### Design Guidelines by App Type

| App Type | Style | Colors | Icon Ideas |
|----------|-------|--------|------------|
| Finance/Banking | Minimal, professional | Blue, green, dark | Letter, shield, chart |
| Productivity | Clean, modern | Purple, blue | Checkmark, layers, grid |
| Social/Community | Friendly, warm | Orange, pink | Heart, people, chat |
| Developer Tools | Technical, dark | Dark gray, cyan | Terminal, brackets, code |
| E-commerce | Bold, trustworthy | Orange, blue | Cart, bag, tag |
| Health/Fitness | Energetic, fresh | Green, teal | Heart, leaf, pulse |
| Education | Approachable | Blue, yellow | Book, cap, lightbulb |

### Color Suggestions

```typescript
const colorSchemes = {
  professional: "linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%)",
  creative: "linear-gradient(135deg, #ec4899 0%, #8b5cf6 100%)",
  growth: "linear-gradient(135deg, #059669 0%, #10b981 100%)",
  energy: "linear-gradient(135deg, #ea580c 0%, #f59e0b 100%)",
  trust: "linear-gradient(135deg, #0284c7 0%, #06b6d4 100%)",
  minimal: "#0f172a", // Solid dark
  light: "#f8fafc",   // Solid light with colored icon
};
```

---

## Option B: Generate from Existing Source Image

### Using Sharp

```bash
bun add sharp
```

```typescript
// scripts/generate-favicons.ts
import sharp from "sharp";
import path from "path";

const sizes = [16, 32, 48, 96, 144, 180, 192, 512];
const inputFile = "./source-icon.png"; // 512x512 or larger

async function generateFavicons() {
  for (const size of sizes) {
    await sharp(inputFile)
      .resize(size, size)
      .png()
      .toFile(`./public/icon-${size}.png`);
  }

  // Generate ICO file (16, 32, 48)
  await sharp(inputFile)
    .resize(32, 32)
    .toFile("./public/favicon.ico");

  console.log("Favicons generated!");
}

generateFavicons();
```

---

## Next.js Metadata Configuration

### Full Configuration (app/layout.tsx)

```typescript
import type { Metadata } from "next";

export const metadata: Metadata = {
  icons: {
    icon: [
      { url: "/favicon.ico", sizes: "any" },
      { url: "/icon.svg", type: "image/svg+xml" },
      { url: "/favicon-16x16.png", sizes: "16x16", type: "image/png" },
      { url: "/favicon-32x32.png", sizes: "32x32", type: "image/png" },
    ],
    apple: [
      { url: "/apple-touch-icon.png", sizes: "180x180", type: "image/png" },
    ],
    other: [
      {
        rel: "mask-icon",
        url: "/safari-pinned-tab.svg",
        color: "#5bbad5",
      },
    ],
  },
  manifest: "/site.webmanifest",
};
```

### PWA Manifest (app/manifest.ts)

```typescript
import type { MetadataRoute } from "next";

export default function manifest(): MetadataRoute.Manifest {
  return {
    name: "Brand Name",
    short_name: "Brand",
    description: "Your app description",
    start_url: "/",
    display: "standalone",
    background_color: "#0f172a",
    theme_color: "#6366f1",
    icons: [
      {
        src: "/icon-192.png",
        sizes: "192x192",
        type: "image/png",
      },
      {
        src: "/icon-512.png",
        sizes: "512x512",
        type: "image/png",
      },
    ],
  };
}
```

---

## Dark Mode Support (SVG)

```svg
<!-- public/icon.svg -->
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32">
  <style>
    rect { fill: #000; }
    @media (prefers-color-scheme: dark) {
      rect { fill: #fff; }
    }
  </style>
  <rect width="32" height="32" rx="4"/>
</svg>
```

## File-Based Convention

Next.js automatically handles favicons when placed in `app/`:

```
app/
├── favicon.ico          # Required: shown in browser tab
├── icon.png             # Optional: higher quality icon
├── icon.svg             # Optional: scalable vector icon
├── apple-icon.png       # Required for iOS: 180x180
└── opengraph-image.png  # Social sharing image: 1200x630
```
