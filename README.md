# FSL Associates Website

Production marketing site for **FSL Associates, Inc.** — environmental, civil, and forensic engineering since 1983.

Built with [Astro 6](https://astro.build) + [Tailwind CSS 4](https://tailwindcss.com). Output is static HTML/CSS/JS suitable for on-premise hosting (nginx, Apache, or IIS with static file support).

## Requirements

- **Node.js** 22.12 or newer
- **npm** 10+

## Quick start (local preview)

```bash
cd /path/to/FSL
npm install
npm run dev
```

Open **http://localhost:4321** in your browser.

To preview the production build:

```bash
npm run build
npm run preview
```

## Project structure

```
FSL/
├── public/              # Static assets (images, map, contact handler, robots.txt)
│   ├── api/contact.php  # PHP form handler (copied to dist/ on build)
│   ├── map/             # Leaflet choropleth map assets
│   └── scripts/         # Client-side tooltip glossary
├── server/              # Optional Node.js contact handler + nginx examples
├── src/
│   ├── components/      # Reusable UI (header, footer, forms, hero, map, etc.)
│   ├── data/            # Page content, navigation, projects, tooltips
│   ├── layouts/         # BaseLayout, MinimalLayout
│   ├── pages/           # File-based routes (~38 pages)
│   └── styles/          # Global CSS + Tailwind theme tokens
├── astro.config.mjs
└── package.json
```

## Site map (routes)

| Section | Routes |
|---------|--------|
| Home | `/` |
| About | `/about/history`, `/about/team`, `/about/why-fsl`, `/about/careers`, `/about/insights` + 3 articles |
| Markets | `/markets/lenders`, `/markets/developers`, `/markets/insurers` |
| Services | `/services` + four service detail pages |
| Projects | `/projects` + 18 project detail pages |
| Contact | `/contact` |
| Other | `/404`, `/employee-login` (noindex) |

Dynamic sitemap: **https://fslassociates.com/sitemap.xml** (generated at build time).

## Content updates

Most copy lives in `src/data/` TypeScript files:

| File | Contents |
|------|----------|
| `homepage.ts` | Hero slides, stats, services strip, audience cards |
| `markets.ts` | Lenders / developers / insurers page copy |
| `services-content.ts` | Four service pages |
| `projects.ts` | Project cards and detail pages |
| `team.ts` | Team bios |
| `articles.ts` | News & Insights articles |
| `tooltips.ts` | Acronym glossary (also mirrored in `public/scripts/tooltips.js`) |
| `navigation.ts` | Header and footer links |

After editing content, run `npm run build` and redeploy `dist/`.

## Contact forms

Forms POST JSON to **`/api/contact.php`** on the production server.

1. **PHP (recommended for Apache/nginx + PHP)**  
   - Handler is included in `dist/api/contact.php` after build.  
   - Ensure PHP-FPM or mod_php is enabled and `mail()` or SMTP is configured.  
   - Set `CONTACT_TO=info@fslassociates.com` in the server environment (optional override).

2. **Node.js (alternative)**  
   ```bash
   cd server
   npm install
   SMTP_HOST=... SMTP_USER=... SMTP_PASS=... npm start
   ```  
   Proxy `/api/contact` to `http://127.0.0.1:3001/api/contact` (see `server/nginx-contact.conf.example`).

If the server handler is unavailable, the form falls back to opening the visitor's email client (`mailto:info@fslassociates.com`).

The contact page includes an optional **Company** field; other forms use Name, Email, Phone, and Message.

## Interactive features

- **Hero slider** — 5 slides, auto-advance, on homepage (`HeroSlider.astro`)
- **Project map** — Leaflet choropleth of Massachusetts project density (`public/map/`)
- **Tooltips** — Dotted underline on technical terms; definitions from glossary JSON

## SEO & analytics

- Canonical URLs, Open Graph, and Twitter Card meta tags on every page (`BaseLayout.astro`)
- **LocalBusiness** JSON-LD schema on all pages
- `public/robots.txt` — allows crawlers; blocks `/employee-login`
- `sitemap.xml` — auto-generated route list at build time
- **Cookie consent banner** — loads GA4 only after acceptance
- **Google Analytics 4** — set `PUBLIC_GA4_ID=G-XXXXXXXXXX` in `.env` (see `.env.example`)

## Production build & deploy (on-premise)

### 1. Build

```bash
npm ci
npm run build
```

Output is in **`dist/`** — upload the entire folder to the web server document root.

### 2. Web server

**nginx (static + PHP handler):**

```nginx
root /var/www/fsl;
index index.html;
location / {
    try_files $uri $uri/ $uri/index.html =404;
}
location ~ ^/api/contact\.php$ {
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_pass unix:/run/php/php8.2-fpm.sock;
}
```

See `server/nginx-contact.conf.example` for a full SSL example.

**Apache:** enable `mod_rewrite` and point DocumentRoot to `dist/`. Ensure `.php` files in `api/` are executed.

### 3. SSL & DNS

- Obtain an SSL certificate for `fslassociates.com`
- Point DNS A/AAAA records to the on-premise server
- Use a **staging subdomain** (e.g. `staging.fslassociates.com`) for client review before cutover

### 4. Post-launch checklist

- [ ] Contact form delivers to `info@fslassociates.com`
- [ ] Submit sitemap in Google Search Console
- [ ] Set `PUBLIC_GA4_ID` and verify analytics
- [ ] Run Lighthouse on homepage (target 90+ mobile/desktop)
- [ ] Test all nav links and project detail pages

## Performance notes

- Static output — no server runtime required for pages
- Map and hero slider JS load only on pages that use them
- Use WebP/optimized images in `public/images/` where possible
- Leaflet CSS/JS loaded from CDN on map section only

## Commands

| Command | Action |
|---------|--------|
| `npm install` | Install dependencies |
| `npm run dev` | Dev server at localhost:4321 |
| `npm run build` | Production build → `dist/` |
| `npm run preview` | Serve `dist/` locally |

## Brand tokens

| Token | Value |
|-------|-------|
| Navy | `#1B2A4A` |
| Gold | `#C8923C` |
| Light gray bg | `#f8f9fa` |
| Body text | `#333333` |

Fonts: **Inter** (body), **Source Serif 4** (headings).

## Support

For engineering content changes, edit `src/data/` files. For layout or new pages, modify components in `src/components/` and routes in `src/pages/`.

Technical contact: FSL Associates — info@fslassociates.com | 617-232-0001
