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

## Version indicator

`src/version.ts` exports `VERSION`, rendered as a centered second line inside the download button in `Hero.astro` (class `btn-download__version`).

**Do not hand-edit `src/version.ts`.** It is auto-managed by `.github/workflows/update-version.yml`, which overwrites the file wholesale on each release.

**How it works:**
1. When a release is published in the Farthing repo, `Farthing/.github/workflows/release.yml` sends a `repository_dispatch` event of type `farthing-release` to this repo, with `client_payload.version` set to the new tag (e.g. `v0.4.0`).
2. `.github/workflows/update-version.yml` picks that up, strips the leading `v`, writes `src/version.ts`, and lands it on `main` via an **auto-merged PR** (main is protected to require pull requests, so a direct push is rejected; PRs need 0 approvals, so the workflow opens one and squash-merges it).
3. The merge to `main` triggers DigitalOcean App Platform's auto-redeploy, so the badge updates on the live site within minutes.

The workflow also supports `workflow_dispatch` with a `version` input for manual updates or backfills.

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
