# Kaarjo Technology — website (React)

This is the Kaarjo Technology marketing site rebuilt from a single static
HTML file into a proper React app (Vite + React Router), with an SEO layer
added so the site is built to rank for **"Kaarjo Technology"** / **"Kaarjo
Tech"** searches.

## Run it locally

```bash
npm install
npm run dev        # http://localhost:5173
```

## Build for deployment

```bash
npm run build:static
```

This does three things in order:
1. `npm run sitemap` — regenerates `public/sitemap.xml` from the services/projects data (so it never drifts out of sync with the real pages).
2. `vite build` — produces the production app in `dist/`.
3. `node scripts/prerender.mjs` — visits every route with headless Chromium and writes the fully-rendered HTML into `dist/<route>/index.html`. This is what makes the site properly crawlable: Googlebot and non-JS bots (Facebook/LinkedIn/Slack/Twitter link previews) get real, complete HTML immediately, with the correct `<title>`, description, Open Graph tags, and structured data for that exact page — not one generic set of tags and an empty `<div id="root">`.

**Deploy the contents of `dist/` to your web host as static files.** That folder is the entire production site — upload it to kaarjotech.com's hosting (Netlify, Vercel, Cloudflare Pages, cPanel, S3+CloudFront, etc.). No Node server is required to run it.

If your host needs a client-side-routing fallback for any URL not already in the sitemap (e.g. `/work/some-typo`), configure it to fall back to `/index.html` — every real route already has its own physical `index.html` from the prerender step, so this only matters for genuinely unknown paths.

## Project structure

- `src/data/site.js` — all copy: services, case studies, process steps, "why us" points. Edit content here.
- `src/components/` — shared chrome (Nav, Footer, ContactSection, CtaBand, FaqBlock, ThemeToggle).
- `src/pages/` — `Home.jsx`, `ServicePage.jsx` (generic template for the 6 services), `ProjectPage.jsx` (generic case-study fallback), `pages/projects/` (the four bespoke long-form case studies: Folio, KANF, Assessment AI, GORON X).
- `src/components/Seo.jsx` + `src/lib/seo.js` — the SEO layer (see below).
- `scripts/generate-sitemap.mjs`, `scripts/prerender.mjs` — build-time tooling.

## What changed for SEO

- **Domain fixed everywhere** to `https://kaarjotech.com` (the original file still pointed at `kaarjo.com` in its canonical tag and structured data).
- **Every route has its own title, meta description, canonical URL, Open Graph/Twitter tags, and JSON-LD structured data** (`src/components/Seo.jsx`), instead of one static set of tags shared by the whole site. Titles and descriptions are written to include "Kaarjo Technology" / "Kaarjo Tech" naturally.
- **Organization + WebSite JSON-LD** on every page (name, alternate names "Kaarjo Tech"/"Kaarjo", logo, address, phone, email) plus **Service** schema on service pages, **CreativeWork** schema on case studies, and **BreadcrumbList** schema on subpages — this is what lets Google show a knowledge-panel-style entity for brand searches.
- **Clean, crawlable URLs** (`/services/mvp-development`, `/work/folio`, etc.) replacing the old `#/services/mvp-development` hash-based routing — hash fragments are not reliably indexed as separate pages.
- **Static prerendering** (`scripts/prerender.mjs`) so every page's full content and tags exist in the raw HTML, not only after JavaScript runs.
- **`sitemap.xml`** (auto-generated from real data, so it can't go stale) and **`robots.txt`** pointing to it.
- **A real 404 page** with `noindex` instead of the old behavior of silently rendering the homepage for any unmatched URL (which confuses Google — "soft 404s").
- **Descriptive `alt` text** on every image and a correct single-`<h1>`-per-page heading hierarchy throughout (the original template didn't consistently use real heading levels).
- **A branded Open Graph share image** (`public/og-cover.png`) so links shared on Facebook/LinkedIn/WhatsApp/Slack show a proper preview card instead of nothing.

## After you deploy — this part matters just as much

Code changes get the site *ready* to rank; they don't make Google index it by
themselves. Once `dist/` is live on kaarjotech.com:

1. **Google Search Console** ([search.google.com/search-console](https://search.google.com/search-console)) — add the property, verify ownership, and submit `https://kaarjotech.com/sitemap.xml`. Then use "Request Indexing" on the homepage. This is the single biggest lever for getting indexed quickly.
2. **Google Business Profile** — create/claim one for "Kaarjo Technology" with your Chattogram address. This is what usually makes a company's own site + map listing show up prominently for a branded search.
3. **Bing Webmaster Tools** — same idea as Search Console, takes 5 minutes, free extra traffic.
4. **Consistent NAP (Name/Address/Phone) everywhere** — LinkedIn, Facebook page, any directory listing — should all say "Kaarjo Technology", the same address, and the same phone number as the website. Inconsistency actively hurts local/brand SEO.
5. **A few backlinks** — a LinkedIn company page, a Facebook business page, and any partner/client site linking to kaarjotech.com meaningfully speed up how fast Google trusts and ranks a new or recently-changed domain for its own brand name.
6. Ranking for your *own company name* is normally not hard once the above is done — the main risk is simply not being indexed yet. Steps 1–2 fix that directly.
