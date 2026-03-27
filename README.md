# AMMD WEB — Sheet Metal Tools & Web Platform

> Free, browser-based tools and a public website for a company manufacturing and selling sheet metal products for the construction industry.

---

## Overview

**AMMD WEB** is a collection of static web applications and tools built for a sheet metal products company operating in the construction sector. The primary audience includes roofing contractors, facade installers, metal service centers, and quantity surveyors.

All tools are built as plain static HTML/CSS/JavaScript — no framework, no CMS, no build tooling overhead. The entire platform is deployed via **Cloudflare Pages** and served globally with zero server-side infrastructure.

---

## Projects

### 🔧 CutTool — Sheet Metal Coil Nesting Calculator

**Live:** [cuttool.app](https://cuttool.app)

A free, browser-based nesting calculator that helps professionals minimize waste when cutting sheet metal coils into profiles of varying lengths.

**Key features:**
- Strict and Combined nesting algorithms
- Metric and imperial unit support
- Light/dark theme
- CSV export and print-to-PDF
- Six languages: English, Serbian, German, French, Italian, Spanish
- No login, no backend — runs entirely in the browser

**Technical highlights:**
- All internal math uses integer millimeters (avoids floating-point drift)
- DM Sans typeface with `font-variant-numeric: tabular-nums` for precise number alignment
- Inline JS color logic for waste visualization (not CSS class abstraction)

---

### 📐 Sheet Metal Roll/Coil Material Estimator

**Files:** `kalkulator-lima.html` (SR) / `nesting-calculator-en.html` (EN)

A standalone single-file calculator for estimating material quantities from sheet metal rolls and coils. Originally built as a Serbian-language tool, with an English version added later.

**Repo:** [`andrijad/Sheet-Metal-Nesting-Calculator`](https://github.com/andrijad/Sheet-Metal-Nesting-Calculator)

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Plain HTML5, CSS3, Vanilla JavaScript |
| Fonts | DM Sans (Google Fonts) |
| Static site generation | Python (`gen_pages.py`, `gen_content.py`, `langs.json`) |
| Image generation | cairosvg (SVG → PNG) |
| Deployment | Cloudflare Pages (Free plan) |
| DNS | Cloudflare |
| Analytics | Google Analytics 4 (direct script tag in `<head>`) |
| Version control | GitHub |

---

## Repository Structure

```
/
├── index.html                   # Default language landing page
├── en/                          # English locale
├── sr/                          # Serbian locale
├── de/                          # German locale
├── fr/                          # French locale
├── it/                          # Italian locale
├── es/                          # Spanish locale
├── assets/
│   ├── css/
│   ├── js/
│   └── img/
│       └── og-image.png         # OG image (1200×630)
├── _headers                     # Cloudflare: CSP, HSTS, cache headers
├── _redirects                   # Cloudflare: trailing slash normalization
├── robots.txt
├── sitemap.xml
├── gen_pages.py                 # Static site generator
├── gen_content.py               # Multilingual content generator
└── langs.json                   # Language strings
```

---

## SEO & Metadata

Each page includes a full set of structured metadata:

- **hreflang** tags for all six languages
- **Canonical URLs**
- **Schema.org markup:** `SoftwareApplication`, `FAQPage`, `WebPage`
- **Open Graph** and **Twitter Card** meta tags
- **OG image** (1200×630 PNG)

---

## Deployment

The site deploys automatically via **GitHub → Cloudflare Pages**.

| Setting | Value |
|---|---|
| Build command | *(none — pure static)* |
| Output directory | `/` (root) |
| Domain | `cuttool.app` (Cloudflare DNS) |

### Cloudflare Configuration

**`_headers`** — security and cache:
```
/*
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Strict-Transport-Security: max-age=31536000; includeSubDomains
  Content-Security-Policy: ...
```

**`_redirects`** — canonical URL normalization:
```
/en/  /en  301
...
```

> **Note:** Cloudflare Zaraz is not used — it requires the Pro plan. GA4 is injected directly as a script tag.

---

## Analytics

Google Analytics 4 is implemented via direct script tag in `<head>` on all pages.

**Measurement ID:** Set `G-XXXXXXXXXX` in the `<head>` of each generated page.

To update the Measurement ID across all pages, edit the value in `gen_pages.py` and regenerate.

---

## Multilingual Content

Language content is managed via `langs.json`. To add or update translations:

1. Edit `langs.json` — add or modify keys for the target language
2. Run `python gen_pages.py` to regenerate all locale pages
3. Verify output in the corresponding locale directory

Supported locales: `en`, `sr`, `de`, `fr`, `it`, `es`

---

## Local Development

No build step required. Open any `.html` file directly in a browser, or serve the root locally:

```bash
# Python 3
python -m http.server 8080

# Then open:
# http://localhost:8080
```

---

## Roadmap

- [x] Core nesting calculator (Strict + Combined algorithms)
- [x] Six-language support
- [x] Cloudflare Pages deployment
- [x] Full SEO / Schema markup
- [x] CSV export and print-to-PDF
- [ ] GA4 Measurement ID integration
- [ ] Print-ready quote / predračun feature *(deferred — pending company details)*

---

## License

All rights reserved. © AMMD. Tools are free to use; source code is not open for redistribution without permission.

---

## Contact

For business inquiries or feedback, visit [cuttool.app](https://cuttool.app).
