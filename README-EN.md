# Main Site Template

A zero-build **static main site / hub template**: out-of-the-box, dark/light auto, mobile-friendly, multilingual (ZH/EN/JA), card-style project navigation, scroll-snap sections with entrance animations, subsetted Chinese fonts, and on-demand background loading. Perfect for aggregating your sub-sites, tools, and blog entry points. 

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

---

## ✨ Features at a glance

* **Zero dependencies / zero build**
  Pure `index.html` + local assets. No Node, bundlers, frameworks, or backend. Works on any static host (Vercel / GitHub Pages / Nginx / Caddy).

* **Auto dark/light + smart background selection**
  Uses `prefers-color-scheme` and device DPR/resolution to choose **Light/Dark** and **1280/1920/2560/3840** background variants—crisp yet bandwidth-friendly. Recomputes on resize with debouncing.

* **Subsetted Chinese fonts (self-hosted)**
  Pre-split `.woff2` subsets in `fonts/` with `unicode-range` and `font-display: swap` for fast, stable CJK rendering. Privacy-friendly (no third-party CDNs).

* **Scroll-snap sections & entrance animations**
  `scroll-snap` for page-at-a-time scrolling; `IntersectionObserver` for per-section transitions. Respects accessibility preference: if the user enables **reduced motion**, animations are disabled.

* **Multilingual switcher (ZH/EN/JA)**
  Pure-frontend i18n: a `translations` object and `data-i18n` / `data-i18n-aria-label` attributes auto-replace copy and ARIA labels; includes language menu and visibility logic.

* **Glassmorphism cards / dark neon gradients**
  CSS variables + `backdrop-filter` + `color-mix` produce airy glass cards; themeable via a single variable set.

* **Accessibility & semantic structure**
  Uses `aria-` attributes (e.g., button hints, menu states), keyboard/reader-friendly; semantic sections/headings.

* **Minimal SEO**
  Built-in `<meta name="description">`; extend with Open Graph / Twitter Cards as needed.

---

## 🗂 Directory structure

```
.
├─ index.html            # Main page (inline styles, scripts, layout, i18n)
├─ assets/
│  ├─ background/        # Backgrounds (Light/Dark × 1280/1920/2560/3840)
│  └─ favicon.svg        # Site icon (with favicon1.svg as an alternate)
└─ fonts/
   ├─ style.css          # Aggregates all subset fonts
   ├─ *.css              # Per-weight subsets (with unicode-range)
   └─ files/*.woff2     # Font subset files
```

> Note: resources referenced in `index.html` use **absolute paths** (e.g., `/assets/...`, `/fonts/style.css`). For **subpath** hosting like `https://user.github.io/repo/`, switch to relative paths or use the GitHub Actions auto-rewrite workflow below.

---

## 🔧 Usage guide

### 1) Local preview (no install)

* Just open `index.html` in a browser.
* If you want a local server (for path simulation etc.):

  * Python: `python3 -m http.server 8787`
  * Node (no project deps, global only): `npx serve .` or `npx http-server -p 8787`

### 2) Customization

* **Site cards**
  In the “Experiments” `<section>` of `index.html`, each card is an `<article class="card">`. Edit:

  * Title `<h3 class="title">`
  * Description `<p class="text" data-i18n="...">`
  * Link `<a class="go" href="...">`
  * Tags `<span class="tag" ...>`

* **Copy & languages**
  Maintain copy inside the `translations` object (second-to-last `<script>` in `index.html`).

  * Add `data-i18n="key"` on elements to auto-inject copy.
  * For ARIA labels, use `data-i18n-aria-label="key"`.
  * To add a new language:

    1. Add its code to `languages=['zh','en','ja']` (e.g., `'fr'`);
    2. Provide `translations.fr = {...}` with matching keys;
    3. Add a menu item: `<button data-language-option="fr">Français</button>`.

* **Light/Dark backgrounds**
  Place images in `assets/background/` using this naming convention:

  ```
  Light-1280x1280-hq.jpg
  Light-1920x1920-hq.jpg
  Light-2560x2560-hq.jpg
  Light-3840x3840-hq.jpg
  Dark-1280x1280-hq.jpg
  ...
  ```

  The script picks the best match by DPR/window size. You can change `sizes=[1280,1920,2560,3840]` or `basePath`.

* **Site icon**
  Replace `assets/favicon.svg`; update `<link rel="icon" href="/assets/favicon.svg">` if switching to `.png`.

* **Theme & styles**
  Core colors, blur, radii, shadows, and grid gaps live under `:root { --vars }` for centralized theming.

* **SEO**
  Add the following `<meta>` as needed:

  ```html
  <meta property="og:title" content="Your Site Title" />
  <meta property="og:description" content="One-line description" />
  <meta property="og:type" content="website" />
  <meta property="og:image" content="/assets/og-cover.png" />
  <meta name="twitter:card" content="summary_large_image" />
  ```

  If deploying under a subpath, consider relative or absolute-domain URLs for `/assets/...`.

---

## ▲ Preferred deployment: Vercel

### Option A: One-click from Vercel dashboard

One-click deploy:
[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Flzray-universe%2Fmain-website&project-name=main-website&repository-name=main-website&demo-url=https%3A%2F%2Flzray.com)

1. Push the repo to GitHub / GitLab.
2. Open [Vercel](https://vercel.com/) → **Add New… → Project** → select your repo.
3. Set:

   * **Framework Preset**: `Other`
   * **Build Command**: leave empty (no build)
   * **Output Directory**: `/` (repo root)
4. Click **Deploy**.
5. (Optional) Add a custom domain; Vercel auto-provisions HTTPS.

### Option B: `vercel.json`

Create `vercel.json` at the repo root to optimize caching and URLs:

```json
{
  "cleanUrls": true,
  "trailingSlash": false,
  "headers": [
    {
      "source": "/assets/(.*)\\.(jpg|jpeg|png|svg|webp)",
      "headers": [{ "key": "Cache-Control", "value": "public, max-age=31536000, immutable" }]
    },
    {
      "source": "/fonts/(.*)\\.(woff2|woff)",
      "headers": [{ "key": "Cache-Control", "value": "public, max-age=31536000, immutable" }]
    },
    {
      "source": "/index.html",
      "headers": [{ "key": "Cache-Control", "value": "no-cache" }]
    }
  ]
}
```

### Option C: Vercel CLI (no build)

```bash
npm i -g vercel
vercel --prod
```

---

## Alternative deployment: GitHub Actions → GitHub Pages

> **Important: pathing**
> The template uses **absolute** asset paths (`/assets`, `/fonts`…).
>
> * If your Pages site is at the **root** (e.g., `https://user.github.io/` or a custom root domain), publish directly.
> * If hosted under a **project subpath** (e.g., `https://user.github.io/repo/`), use the **“rewrite absolute paths to relative”** workflow (Option B below) or your assets will 404.

### Option A: Deploy to root / custom root domain

1. In your GitHub repo → **Settings → Pages**: set **Source** to **GitHub Actions**.
2. Add `.github/workflows/pages.yml`:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      # Optional: list build artifacts
      - name: List files
        run: ls -la

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: .

      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
```

> Trigger: push to `main` auto-deploys. For the first time, confirm Pages enablement in the Pages settings.

### Option B: Deploy to a **project subpath** (auto-rewrite assets to relative)

Rewrite absolute resource paths in `index.html` to relative ones before upload to avoid 404s.

```yaml
name: Deploy to GitHub Pages (Subpath Safe)

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      # Rewrite absolute "/assets" "/fonts" etc. to "./assets" "./fonts"
      - name: Rewrite absolute asset paths for subpath hosting
        run: |
          # Only index.html is needed (styles/scripts inline there)
          sed -i -E 's|href="/fonts|href="./fonts|g' index.html
          sed -i -E 's|href="/assets|href="./assets|g' index.html
          sed -i -E "s|url\\('/assets|url\\('\\./assets|g" index.html
          sed -i -E 's|src="/assets|src="./assets|g' index.html
          sed -i -E 's|src="/fonts|src="./fonts|g' index.html

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: .

      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
```

> If you later move styles/scripts into standalone `.css` / `.js` files, remember to rewrite absolute paths there too (add more `sed` lines).

---

## FAQ

* **Q: Fonts/backgrounds fail to load on GitHub Pages?**
  A: You’re likely on a project subpath where absolute `/assets` points to the root. Use **Option B** above or switch to a custom root domain.

* **Q: Do animations hurt accessibility?**
  A: The template honors `prefers-reduced-motion`; when set to “reduce,” the script skips animations.

* **Q: Can I compress the backgrounds more?**
  A: Yes—keep the four sizes and naming consistent. WebP works too (update references and cache headers accordingly).

* **Q: Add languages/cards?**
  A: Add matching keys in `translations`, and duplicate an `<article class="card">` with appropriate `data-i18n` keys.

---

## Advanced

* **Caching**: see `vercel.json`. With Nginx/Caddy, set `Cache-Control: public, max-age=31536000, immutable` for `/assets` and `/fonts`.
* **SEO**: add OG/Twitter meta, `robots.txt`, `sitemap.xml`.
* **Analytics**: consider lightweight, privacy-friendly, self-hosted options.

---

## License & credits

* Code: MIT (free to redistribute/modify).
* Fonts & images: rights belong to their original authors; replacing with your own assets is safest.

---

## TL;DR checklist

* Update card links and copy in `index.html` (along with `translations`).
* Replace `assets/background/*` and `assets/favicon.svg`.
* **Vercel**: import repo → Framework “Other” → no build → deploy.
* **GitHub Pages**: use the **Actions** workflows above—root domain can go straight out; **subpaths must use the “rewrite” Option B**.
