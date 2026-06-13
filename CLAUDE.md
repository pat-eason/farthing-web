# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```sh
npm run dev       # dev server at http://localhost:4321
npm run build     # build to ./dist/
npm run preview   # preview built output locally
```

No test suite. No linter configured.

## Architecture

Single-page static Astro site (no UI framework, static output). One page (`src/pages/index.astro`) composes three components on top of a full-viewport animated mesh gradient.

**Component roles:**
- `src/layouts/Base.astro` — `<head>`, OG/meta tags, JSON-LD schema, font imports, global CSS
- `src/components/MeshBackground.astro` — fixed `<canvas>` behind content; imports `whatamesh` dynamically and calls `new Gradient().initGradient('#gradient-canvas')`; respects `prefers-reduced-motion` (hides canvas, shows static CSS gradient fallback); handles dark mode by replacing the canvas on `change` so the old RAF loop detaches
- `src/components/Hero.astro` — app icon, headline, tagline, download button
- `src/components/Carousel.astro` — Embla screenshot carousel with prev/next arrow buttons, `loop: true`

**Config:** `src/config.ts` holds `DOWNLOAD_URL`, `GITHUB_URL`, and `AUTHOR` — the only place to update those values.

**Styling:** Single `src/styles/global.css` with CSS custom properties for the palette (`--color-ink`, `--color-surface`, `--color-accent`, etc.) and frosted-glass card surfaces via `backdrop-filter`. No Tailwind, no CSS modules. All component styles live in `global.css`.

**Fonts:** Fraunces (display headline, weights 500/600) + Inter (body/UI, weights 400/500), self-hosted via `@fontsource/*` — no external CDN.

## Swapping in real assets

| File / constant | What to replace |
|---|---|
| `public/app-icon.png` | App icon (512×512 PNG) |
| `public/screenshots/*.png` | Screenshots shown in the carousel |
| `public/og-image.png` | Social share preview (1200×630) |
| `src/config.ts` → `DOWNLOAD_URL` | Real GitHub Releases asset URL |
| `src/config.ts` → `GITHUB_URL` | GitHub profile URL |

## Deploy

DigitalOcean App Platform static site. Build command: `npm run build`, output directory: `dist`. Pushes to `main` redeploy automatically.
