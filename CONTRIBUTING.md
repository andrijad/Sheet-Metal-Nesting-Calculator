# Contributing & Development Guide

This document covers the conventions, patterns, and workflow used across the AMMD WEB project.

---

## Philosophy

- **Static-first.** No frameworks, no bundlers, no CMS. Every tool is a self-contained HTML file or a set of static assets.
- **No runtime dependencies.** All logic runs in the browser with vanilla JS.
- **Integer math only.** All internal calculations use integer millimeters to avoid floating-point rounding errors.
- **Iterative.** Build → test → refine. Small, specific, correct changes over large rewrites.

---

## Development Workflow

### 1. Clone the repository

```bash
git clone https://github.com/andrijad/<repo-name>.git
cd <repo-name>
```

### 2. Serve locally

No build step needed:

```bash
python -m http.server 8080
# Open http://localhost:8080
```

### 3. Edit content or logic

- **UI/layout:** Edit the relevant `.html` file directly.
- **Multilingual content:** Edit `langs.json`, then run the generator:

```bash
python gen_pages.py
```

- **Algorithms:** Edit the JS section inside the relevant `.html` file. Keep math in integer mm.

### 4. Push and deploy

```bash
git add .
git commit -m "describe change"
git push origin main
```

Cloudflare Pages picks up the push automatically. No build command — root is served directly.

---

## Code Conventions

### HTML

- All pages must include: `lang` attribute, canonical URL, hreflang tags, OG/Twitter meta, Schema markup.
- GA4 script tag goes in `<head>`, before `</head>`.
- No inline `style` attributes for theme logic — use JS to set CSS variables or classes on `<body>`.

### CSS

- Light/dark theme is toggled via a class on `<body>`.
- Use `font-variant-numeric: tabular-nums` for all numeric output elements.
- Font family: **DM Sans** only. No secondary monospace font.

### JavaScript

- Waste color logic uses **inline JS**, not CSS class abstractions. This is intentional — keeps visual logic co-located with data.
- All nesting calculations operate on integers (millimeters). Convert to display units only at render time.
- No ES modules, no imports. Single-file scripts only.

### Python (gen_pages.py / gen_content.py)

- Reads `langs.json` as the single source of truth for all translatable strings.
- Outputs one HTML file per language into the corresponding locale subdirectory.
- Script should be idempotent — running it twice produces the same output.

---

## File Naming

| Pattern | Use |
|---|---|
| `index.html` | Default locale landing page |
| `{locale}/index.html` | Locale-specific page |
| `_headers` | Cloudflare Pages HTTP headers |
| `_redirects` | Cloudflare Pages redirect rules |
| `langs.json` | All translatable UI strings |
| `gen_pages.py` | Static site generator entry point |

---

## Adding a New Language

1. Add the new locale code and all string keys to `langs.json`
2. Add hreflang entry to `gen_pages.py`
3. Add the locale to `sitemap.xml`
4. Run `python gen_pages.py`
5. Verify the output directory and spot-check the generated HTML

---

## Deployment Checklist

Before pushing to `main`:

- [ ] All locale pages regenerated (`python gen_pages.py`)
- [ ] GA4 Measurement ID (`G-XXXXXXXXXX`) present in `<head>`
- [ ] `robots.txt` and `sitemap.xml` up to date
- [ ] `_headers` and `_redirects` correct
- [ ] OG image present at `assets/img/og-image.png` (1200×630 px)
- [ ] Canonical URLs correct for all pages
- [ ] Tested in both light and dark theme
- [ ] Tested on mobile viewport

---

## Notes on Cloudflare Pages

- **Free plan** — Cloudflare Zaraz is not available. Use direct GA4 script tag.
- **No build command** — set output directory to `/` (root).
- **Trailing slash normalization** is handled by `_redirects`, not Cloudflare settings.
- DNS is also managed on Cloudflare — domain `cuttool.app`.
