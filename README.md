# farthing-web

Brochure site for [Farthing](https://github.com/OWNER/REPO) — a macOS app for tracking every farthing spent in Claude Code.

Built with [Astro](https://astro.build), [Embla Carousel](https://www.embla-carousel.com), and [whatamesh](https://whatamesh.vercel.app) for the animated gradient.

## Local development

```sh
npm install
npm run dev        # dev server at http://localhost:4321
npm run build      # build to ./dist/
npm run preview    # preview the built output locally
```

## Swapping in real assets

| File | What to replace |
|------|-----------------|
| `public/app-icon.png` | App icon (512×512 PNG or SVG) |
| `public/screenshots/*.png` | Screenshots shown in the carousel |
| `public/og-image.png` | Social share preview (1200×630) |
| `src/config.ts` → `DOWNLOAD_URL` | Real GitHub Releases asset URL |
| `src/config.ts` → `GITHUB_URL` | Your GitHub profile URL |

## Deploy to DigitalOcean App Platform

1. Push this repo to GitHub.
2. In the DO console: **Apps → Create App → GitHub** → pick this repo.
3. DO auto-detects Astro. Confirm:
   - **Build command:** `npm run build`
   - **Output directory:** `dist`
   - **Type:** Static Site
4. Click Deploy. Pushes to `main` redeploy automatically.
5. (Optional) Add a custom domain under **Settings → Domains**.
