# Farthing — Brochure Website Plan

## Context
Farthing is a new macOS app for tracking every farthing spent in Claude Code. It
needs a simple, elegant brochure/landing site to introduce the app and drive
downloads. The site is purely marketing — one page, a headline, a short tagline, a
screenshot carousel, and a single Download button pointing at a GitHub Releases
asset. It should feel calm and refined: serif display headline, clean sans body,
and a soft pastel mesh gradient that slowly morphs in the background.

The current directory `/Users/patrickeason/Projects/farthing-web` is empty — this
is a greenfield build. Goal: a static site that is trivial to maintain and deploy
to DigitalOcean App Platform.

## Decisions (confirmed with user)
- **Framework:** Astro (static output, zero JS by default).
- **Typography:** Serif display (Fraunces) for the headline + Inter for body/UI.
- **Background:** WebGL mesh gradient via `whatamesh` (Stripe-style, pastel).
- **Carousel:** Embla Carousel (framework-agnostic, tiny, accessible).
- **Deploy:** DigitalOcean App Platform static site, connected to a Git repo.

## Tech stack
- **Astro** (`npm create astro@latest` → minimal/empty template, no UI framework).
- **embla-carousel** — vanilla carousel for the screenshot gallery.
- **whatamesh** — vanilla WebGL animated mesh gradient (MIT, single file). Colors
  set through CSS custom properties `--gradient-color-1..4`, paired with the pastel
  palette from the inspiration image (soft cyan, mint, lavender-white, pink).
- **Fonts:** Fraunces + Inter, self-hosted via `@fontsource/fraunces` and
  `@fontsource/inter` (no external font CDN; faster + privacy-friendly).
- No Tailwind — a single scoped CSS file keeps a one-page site simple. (Can revisit
  if the user prefers Tailwind.)

## Project structure
```
farthing-web/
├── astro.config.mjs          # output: 'static' (default)
├── package.json
├── public/
│   ├── favicon.svg
│   ├── og-image.png          # social share preview (user-supplied later)
│   └── screenshots/          # carousel images (user-supplied)
│       ├── sessions.png
│       ├── ...
├── src/
│   ├── layouts/
│   │   └── Base.astro         # <head>, meta/OG tags, font imports, global CSS
│   ├── components/
│   │   ├── MeshBackground.astro  # <canvas> + whatamesh init + palette vars
│   │   ├── Hero.astro            # logo, "Farthing", tagline, Download button
│   │   └── Carousel.astro        # Embla screenshot carousel + prev/next arrows
│   ├── pages/
│   │   └── index.astro        # composes the single landing page
│   └── styles/
│       └── global.css         # typography scale, layout, palette, reduced-motion
└── README.md                  # local dev + deploy notes
```

## Key implementation details

### Layout (matches mockup)
Single centered column on top of the full-viewport gradient:
1. **Hero** — round app logo, `Farthing` headline (Fraunces, large), tagline
   "A macOS app for tracking every farthing spent in Claude Code", and a primary
   **Download** button (right-aligned on wide screens, stacked on mobile).
2. **Carousel** — a framed window screenshot with left/right circular arrow
   buttons, exactly like the mockup. Dots/indicator below.
3. **Footer** — quiet maker signature "Built by Patrick Eason" linking to his
   GitHub profile, alongside `© 2026 Patrick Eason`. See "Maker presence" below.

### MeshBackground.astro
- Fixed full-screen `<canvas id="gradient-canvas">` behind content (`z-index:-1`).
- Inline module script imports `whatamesh`, instantiates `new Gradient()` and
  calls `.initGradient('#gradient-canvas')`.
- Palette via CSS vars on the canvas: derived from `pastel-background.webp`
  (`#bfeef5` cyan, `#cdeecd` mint, `#f3f0fb` lavender-white, `#f7c7e8` pink).
- Wrap init in `prefers-reduced-motion` check; if reduced, render a static CSS
  gradient fallback instead of animating.

### Carousel.astro
- Markup: `.embla > .embla__container > .embla__slide*` with `<img>` per shot.
- Inline module script imports `embla-carousel`, mounts on the viewport, wires the
  two circular arrow buttons and dot indicators. `loop: true`, keyboard + drag.
- Images lazy-loaded except the first; fixed aspect ratio to avoid layout shift.

### Typography & styling (global.css)
- Headline: Fraunces (optical-size high, weight ~500–600).
- Body/UI: Inter.
- Restrained scale, generous line-height, max content width ~640px hero / wider
  carousel. Frosted-glass card surfaces (`backdrop-filter: blur`) so the gradient
  reads through gently. Download button: solid accent (mockup blue `#2f6fed`) with
  hover/active states.

### Download button
- `<a href="DOWNLOAD_URL" download>` — `DOWNLOAD_URL` kept as a single constant in
  `index.astro` (or `src/config.ts`) so it's a one-line change later. Placeholder
  points to `https://github.com/OWNER/REPO/releases/latest` until the real GitHub
  Releases asset URL is supplied.

### Maker presence (subtle self-promotion)
Advertise Patrick as the maker without it feeling promotional. Two layers:
- **Visible — footer signature:** a single quiet line, "Built by Patrick Eason"
  with his name linking to his GitHub profile, plus `© 2026 Patrick Eason`. Small,
  muted type at the bottom of the page. (GitHub is the only requested link for now;
  the footer is structured so personal-site / X / email links can be added later.)
- **Invisible — SEO/identity layer** (in `Base.astro` `<head>`):
  - `<meta name="author" content="Patrick Eason">`.
  - JSON-LD `SoftwareApplication` schema with a `creator`/`author` `Person` block
    (name: Patrick Eason, `sameAs`: GitHub profile URL) so search engines and social
    shares attribute Farthing to him. Open Graph card reads "by Patrick Eason".
  These add zero visible clutter while tying the app to his name in search/sharing.

## Assets needed from user (later — placeholders used meanwhile)
- App logo (the puffin/duck roundel from the mockup), ideally SVG or 512px PNG.
- Screenshot images for the carousel (the Sessions view etc.).
- Final GitHub Releases download URL.
- GitHub profile URL/username (for the footer signature + JSON-LD `sameAs`).
- Optional: custom domain (e.g. `farthing.app`) for DO.

## Deployment — DigitalOcean App Platform (static site)
1. `git init` the project, push to GitHub (repo is not yet a git repo).
2. DO console → Apps → Create → pick the repo.
3. DO auto-detects Astro: Build command `npm run build`, Output dir `dist`.
   Configure as a **Static Site** component (free tier, auto HTTPS).
4. Add custom domain + CNAME when ready. Pushes to the main branch auto-deploy.
5. Add `README.md` documenting `npm install && npm run dev` and the deploy flow.

## Verification
- `npm run dev` → open localhost: confirm gradient animates and morphs slowly,
  headline/tagline render in the right fonts, Download button links correctly,
  carousel advances via arrows/dots/drag/keyboard and loops.
- Toggle macOS "Reduce Motion" → confirm gradient falls back to static, no errors.
- Responsive check at mobile / tablet / desktop widths (hero stacks, carousel fits).
- `npm run build && npm run preview` → confirm `dist/` builds clean and the static
  output works without a dev server.
- Lighthouse pass (performance/accessibility) on the built output as a sanity check.

## Open / future
- Real assets + download URL swap-in.
- Optional extras not in scope now: feature highlights section, pricing, changelog.
