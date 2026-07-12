# Arcan Landing Page — Design Spec

**Date:** 2026-07-12
**Source design:** `Arcan Landing Explorations v2.html` (design-canvas export in `Arcan(1).zip`), frame **"v2 · full page"** for desktop, frame **"v2 · mobile"** (390px) for the responsive treatment. Hero explorations B and C are explicitly out of scope.

## Goal

A production static landing page for Arcan (local-first, end-to-end encrypted messenger), hosted on GitHub Pages from a new public repository `arcan-website` on the owner's personal GitHub account. No build step, no JavaScript, no third-party requests.

## Repository layout

```
arcan-website/
├── index.html          # entire page — single file (no build step to assemble partials)
├── css/
│   ├── fonts.css       # @font-face declarations only
│   ├── tokens.css      # Nox design tokens (trimmed from nox-tokens.css)
│   └── style.css       # page styles (merged/trimmed landing-shared.css + landing-v2.css)
├── fonts/              # self-hosted latin woff2: JetBrains Mono 400/500/600/700, Inter 300/400/500/600/700
├── assets/
│   └── mark-full-white.svg   # brand mark (from zip brand/svg/)
├── favicon.ico + apple-touch-icon.png + favicon-16/32/48, icon-512  # from zip brand/
├── .nojekyll           # disable Jekyll processing on Pages
├── .gitignore          # excludes Arcan(1).zip and arcan-extracted/
├── README.md           # project intro + "Pending links" stub table
└── docs/superpowers/   # this spec + implementation plan
```

## Page structure (document-flow order, content verbatim from the v2 full-page frame)

1. **Nav** — brand (mark + "arcan"), links: features / download / github, right side: "open in browser" ghost button + "download" primary button. Section links (`features`, `download`) become in-page anchors.
2. **Hero** — star field, orbit stage with satellite around the Arcan mark, "arcan" wordmark, meta line, headline "the messenger with nothing to leak" (gradient on last phrase), sub paragraph, CTA row (download for android / open in browser), center rule, platform line.
3. **`// why arcan`** — three numbered features (end-to-end encrypted, open source & self-hostable, local-first with jazz.tools link) beside the pure-HTML phone mockup (chat list, tabs, FAB) with caption "arcan on android · v0.4".
4. **`// download`** — Android card (v0.4.2 chip, apk copy, download + checksums actions), browser card, four coming-soon tiles (linux/windows/macos/ios), then two-column: "install on android" steps and "verify the build" terminal block.
5. **Footer** — brand, © 2026 nox-decima, "no trackers on this page", links: nox-decima.dev / github / mailto:arcan@nox-decima.dev.

Canvas artifacts are dropped: `.frame` absolute positioning, `frame-label` spans, `design_doc_mode` meta, hero explorations B/C, the separate mobile frame (its treatment is folded into media queries).

## Responsive behavior

- Desktop layout matches the 1440px frame; content container is fluid with a max-width of 1440px and scales down gracefully.
- At ~900px: features/phone grid, download grid, and install/verify grid collapse to single column per the mobile frame; spacing tightens to the mobile frame's values.
- Nav below ~700px: links and buttons hide; a burger appears (as in the mobile frame). The burger is a CSS-only disclosure (hidden checkbox or `<details>`) revealing the three nav links — no JavaScript.
- Copy is layout-independent: the desktop frame's text is canonical at every width. The mobile frame's shortened copy variants are a canvas-space concession and are **not** used.

## Assets

- **Fonts:** self-hosted latin-subset woff2 committed to `fonts/`; `font-display: swap`. Google Fonts `@import` and preconnects removed.
- **Icons:** every `<i data-lucide="…">` replaced with inline Lucide SVG (viewBox 24, stroke-width 1.5, currentColor) — icons needed: list, download, lock, cloud-off, settings, message-square, users, plus, smartphone, globe, terminal, monitor, laptop, tablet, mail, plus the GitHub mark already inline in the design.
- **Brand:** `mark-full-white.svg` and favicon set copied from the zip.
- Zero external requests in shipped HTML/CSS (verified by grep).

## Links

| Destination | Treatment |
|---|---|
| Arcan app GitHub repo | Real — exact URL confirmed against the owner's `gh` account before wiring (working guess: `nox-decima/arcan`) |
| nox-decima.dev | Real — `https://nox-decima.dev` |
| jazz.tools | Real — kept as in design |
| mailto:arcan@nox-decima.dev | Real — kept as in design |
| Web app ("open in browser") | **Stub** — `href="#"` + `data-stub` |
| .apk download | **Stub** — `href="#"` + `data-stub` |
| sha256 checksums | **Stub** — `href="#"` + `data-stub` |

Every stub: `data-stub` attribute in HTML, row in README "Pending links" table, and a follow-up item registered via the followup-tracking flow.

## Deployment

- Public repo `arcan-website` on the owner's personal account, created with `gh` (run via `nix-shell -p gh`).
- GitHub Pages: `main` branch, `/` root. Site at `https://<user>.github.io/arcan-website/` until a custom domain is added.

## Verification

1. Serve locally (`python3 -m http.server`), screenshot at 1440px and 390px, compare against the design frames.
2. `grep -rE "https?://" index.html css/` shows only intentional outbound *link hrefs* (github, nox-decima.dev, jazz.tools) — no loaded resources.
3. After push + Pages enable: `curl` the live URL, confirm 200 and correct title; load it in a browser.

## Out of scope

- Custom domain setup, 404 page, SEO/OG meta beyond basics, analytics (deliberately none), the Arcan app itself, other pages from the design zip.
