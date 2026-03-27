# Architecture & Technical Reference

Technical reference for the CutTool nesting calculator and the broader AMMD WEB platform.

---

## Nesting Calculator — Algorithm Overview

The nesting calculator solves the **cutting stock problem**: given a coil of fixed length, find the optimal way to cut pieces of varying lengths to minimize waste.

### Algorithms

#### Strict Nesting

Each coil is filled using only one profile length (or a combination that produces zero waste). Pieces of the same length are grouped, and the number of coils required is computed directly.

- Best when: profile lengths divide evenly into coil length, or when mixing lengths is impractical.
- Output: number of coils per profile length, total waste per profile.

#### Combined Nesting

Multiple profile lengths are combined on a single coil to minimize overall waste. A greedy first-fit decreasing heuristic is applied:

1. Sort profile lengths descending.
2. For each piece, place it in the first coil where it fits.
3. Open a new coil if no existing coil has sufficient remaining length.

- Best when: profile lengths are varied and mixing is acceptable in production.
- Output: coil layouts, per-coil waste, total waste across all coils.

### Integer Math

**All internal calculations use integer millimeters.**

- Input values (whether entered in mm or inches) are converted to integer mm immediately on input.
- Imperial inputs: `Math.round(inches * 25.4)` → integer mm.
- All arithmetic (addition, subtraction, division for waste %) operates on integers.
- Display conversion back to imperial happens only at render time.

This eliminates floating-point accumulation errors that would otherwise appear in waste calculations over many cuts.

---

## Static Site Generator

### Files

| File | Role |
|---|---|
| `langs.json` | Single source of truth for all UI strings in all 6 languages |
| `gen_pages.py` | Reads `langs.json`, renders templates, writes output HTML per locale |
| `gen_content.py` | Generates content blocks (FAQ, feature lists, etc.) from structured data |

### Locale Structure

```
/
├── index.html          ← Default locale (EN)
├── en/index.html
├── sr/index.html
├── de/index.html
├── fr/index.html
├── it/index.html
└── es/index.html
```

Each generated page contains the full app — there is no shared JS bundle loaded across locales. This keeps each page fully self-contained and cache-friendly.

### hreflang

Every page includes a full set of `<link rel="alternate" hreflang="...">` tags pointing to all six locale variants plus `x-default`.

```html
<link rel="alternate" hreflang="en" href="https://cuttool.app/en/" />
<link rel="alternate" hreflang="sr" href="https://cuttool.app/sr/" />
<!-- ... -->
<link rel="alternate" hreflang="x-default" href="https://cuttool.app/" />
```

---

## Schema Markup

Each page includes three Schema.org types as JSON-LD:

### `SoftwareApplication`
Describes CutTool as a free web application — enables rich results in Google Search.

```json
{
  "@type": "SoftwareApplication",
  "name": "CutTool",
  "applicationCategory": "BusinessApplication",
  "operatingSystem": "Web",
  "offers": { "@type": "Offer", "price": "0" }
}
```

### `FAQPage`
Structured FAQ answers for common nesting calculator questions — eligible for FAQ rich results.

### `WebPage`
Standard page metadata: name, description, URL, language.

---

## Cloudflare Pages Configuration

### `_headers`

```
/*
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  Permissions-Policy: geolocation=(), microphone=(), camera=()

/assets/*
  Cache-Control: public, max-age=31536000, immutable
```

- Static assets get a 1-year immutable cache (content-addressed by filename or query string).
- All pages get security headers on every response.

### `_redirects`

Trailing slash normalization — prevents duplicate content indexing:

```
/en/   /en   301
/sr/   /sr   301
/de/   /de   301
/fr/   /fr   301
/it/   /it   301
/es/   /es   301
```

---

## Analytics

GA4 is loaded via a direct script tag in `<head>` on every page.

```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

Replace `G-XXXXXXXXXX` with the actual Measurement ID. The ID is set in `gen_pages.py` and injected at generation time.

**Why not Cloudflare Zaraz?** Zaraz requires a Cloudflare Pro plan or higher. Direct GA4 is simpler, faster, and works on the Free plan.

---

## Typography

- **Font family:** DM Sans (variable, loaded from Google Fonts)
- **Numeric alignment:** `font-variant-numeric: tabular-nums` on all result and input elements
- **No monospace font** — tabular-nums on DM Sans provides consistent column alignment without a secondary typeface

---

## Theme System

Light/dark theme is toggled by adding a class to `<body>`:

```javascript
document.body.classList.toggle('dark');
localStorage.setItem('theme', isDark ? 'dark' : 'light');
```

Preference is persisted in `localStorage` and restored on load.

**Waste visualization colors** are set via inline JavaScript on render — not via CSS utility classes. Color values are computed from waste percentage and applied directly as `element.style.backgroundColor`. This produces smoother, more accurate color gradients than a class-based approach.

---

## OG Image

- **File:** `assets/img/og-image.png`
- **Dimensions:** 1200 × 630 px
- **Generation:** Source is an SVG; converted to PNG using `cairosvg`

```bash
cairosvg og-image.svg -o assets/img/og-image.png -W 1200 -H 630
```

To regenerate after editing the SVG source, run the above command from the project root.
