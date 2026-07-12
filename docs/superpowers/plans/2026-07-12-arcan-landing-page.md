# Arcan Landing Page Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ship the "v2 · full page" Arcan landing design as a zero-JS, zero-third-party static page on GitHub Pages from a new public `arcan-website` repo.

**Architecture:** Single `index.html` (content taken from the design export's v2 frame, `<body class="v2n">` so design selectors apply verbatim) + three single-purpose stylesheets (`css/fonts.css`, `css/tokens.css`, `css/style.css`). Desktop = 1440px centered column; the design's `.mob2` overrides become `@media` rules. No build step — Pages serves `main` root.

**Tech Stack:** Hand-written HTML/CSS, self-hosted woff2 fonts, inlined Lucide SVGs, `git` + `gh` (via `nix-shell -p gh`), GitHub Pages.

**Spec:** `docs/superpowers/specs/2026-07-12-arcan-landing-design.md`
**Source design (read-only, gitignored):** `arcan-extracted/Arcan Landing Explorations v2.html` (lines 21–205 = desktop frame, 266–416 = mobile frame), `arcan-extracted/landing-shared.css`, `arcan-extracted/landing-v2.css`, `arcan-extracted/nox-tokens.css`. Reference screenshot: `arcan-extracted/screenshots/v2-list.png`.

**Conventions for all tasks:** work from repo root `/home/nox/Documents/Projects/Nox/arcan-website`. Missing CLI tools are run via `nix-shell -p <pkg> --run '<cmd>'` — never installed globally.

---

### Task 1: Repo skeleton and brand assets

**Files:**
- Create: `.nojekyll` (empty), `assets/mark-full-white.svg`, `favicon.ico`, `favicon-16.png`, `favicon-32.png`, `favicon-48.png`, `apple-touch-icon-180.png`, `icon-512.png`

- [ ] **Step 1: Copy assets from the extracted design zip**

```bash
mkdir -p assets css fonts
cp arcan-extracted/brand/svg/mark-full-white.svg assets/
cp arcan-extracted/brand/favicon.ico .
cp arcan-extracted/brand/png/favicon-16.png arcan-extracted/brand/png/favicon-32.png arcan-extracted/brand/png/favicon-48.png .
cp arcan-extracted/brand/png/apple-touch-icon-180.png arcan-extracted/brand/png/icon-512.png .
touch .nojekyll
```

- [ ] **Step 2: Verify**

Run: `ls assets/ && file favicon.ico`
Expected: `mark-full-white.svg`; `favicon.ico: MS Windows icon resource`

- [ ] **Step 3: Commit**

```bash
git add .nojekyll assets favicon.ico favicon-16.png favicon-32.png favicon-48.png apple-touch-icon-180.png icon-512.png
git commit -m "Add brand assets, favicons, and .nojekyll"
```

---

### Task 2: Self-hosted fonts

**Files:**
- Create: `fonts/*.woff2` (9 files), `css/fonts.css`

- [ ] **Step 1: Download latin woff2 subsets from google-webfonts-helper**

```bash
nix-shell -p curl unzip --run '
  curl -sL "https://gwfh.mranftl.com/api/fonts/jetbrains-mono?download=zip&subsets=latin&variants=regular,500,600,700&formats=woff2" -o /tmp/jbm.zip &&
  curl -sL "https://gwfh.mranftl.com/api/fonts/inter?download=zip&subsets=latin&variants=300,regular,500,600,700&formats=woff2" -o /tmp/inter.zip &&
  unzip -o /tmp/jbm.zip -d fonts/ && unzip -o /tmp/inter.zip -d fonts/'
ls fonts/
```

Expected: 9 `.woff2` files (4 jetbrains-mono, 5 inter), names like `jetbrains-mono-v24-latin-regular.woff2`. If the API is down, fallback: download the same variants from https://fontsource.org zips. Note the exact filenames for Step 2.

- [ ] **Step 2: Write `css/fonts.css`**

One `@font-face` per file, adjusting `src` filenames to what Step 1 produced:

```css
/* Self-hosted fonts — no third-party requests. Latin subsets. */
@font-face { font-family: 'JetBrains Mono'; font-style: normal; font-weight: 400; font-display: swap; src: url('../fonts/jetbrains-mono-v24-latin-regular.woff2') format('woff2'); }
@font-face { font-family: 'JetBrains Mono'; font-style: normal; font-weight: 500; font-display: swap; src: url('../fonts/jetbrains-mono-v24-latin-500.woff2') format('woff2'); }
@font-face { font-family: 'JetBrains Mono'; font-style: normal; font-weight: 600; font-display: swap; src: url('../fonts/jetbrains-mono-v24-latin-600.woff2') format('woff2'); }
@font-face { font-family: 'JetBrains Mono'; font-style: normal; font-weight: 700; font-display: swap; src: url('../fonts/jetbrains-mono-v24-latin-700.woff2') format('woff2'); }
@font-face { font-family: 'Inter'; font-style: normal; font-weight: 300; font-display: swap; src: url('../fonts/inter-v20-latin-300.woff2') format('woff2'); }
@font-face { font-family: 'Inter'; font-style: normal; font-weight: 400; font-display: swap; src: url('../fonts/inter-v20-latin-regular.woff2') format('woff2'); }
@font-face { font-family: 'Inter'; font-style: normal; font-weight: 500; font-display: swap; src: url('../fonts/inter-v20-latin-500.woff2') format('woff2'); }
@font-face { font-family: 'Inter'; font-style: normal; font-weight: 600; font-display: swap; src: url('../fonts/inter-v20-latin-600.woff2') format('woff2'); }
@font-face { font-family: 'Inter'; font-style: normal; font-weight: 700; font-display: swap; src: url('../fonts/inter-v20-latin-700.woff2') format('woff2'); }
```

- [ ] **Step 3: Commit**

```bash
git add fonts css/fonts.css
git commit -m "Self-host JetBrains Mono and Inter (latin woff2)"
```

---

### Task 3: Design tokens stylesheet

**Files:**
- Create: `css/tokens.css`
- Source: `arcan-extracted/nox-tokens.css` (263 lines)

- [ ] **Step 1: Copy tokens minus the Google Fonts import**

Copy `arcan-extracted/nox-tokens.css` to `css/tokens.css` **verbatim except**: delete the `@import url('https://fonts.googleapis.com/...')` line (line 12) and the 6-line comment block above it referencing Google Fonts CDN (replace with `/* Fonts are self-hosted — see fonts.css */`). Everything else (palette, type scale, spacing, radii, shadows, motion, semantic element styles) stays byte-identical.

- [ ] **Step 2: Verify no external URLs**

Run: `grep -n "http" css/tokens.css`
Expected: no output.

- [ ] **Step 3: Commit**

```bash
git add css/tokens.css
git commit -m "Add Nox design tokens (self-hosted fonts, no CDN import)"
```

---

### Task 4: Page stylesheet (`css/style.css`)

**Files:**
- Create: `css/style.css`
- Sources: `arcan-extracted/landing-shared.css`, `arcan-extracted/landing-v2.css`

Assemble in this order:

- [ ] **Step 1: Base + page container (new code)**

```css
/* Arcan landing page — merged from landing-shared.css + landing-v2.css,
   canvas frames removed, responsive media queries added. */
* { box-sizing: border-box; }
html, body { margin: 0; }
.page {
  max-width: 1440px;
  margin: 0 auto;
  background: var(--nox-void);
  font-family: var(--font-body);
  color: var(--nox-text);
}
@media (min-width: 1442px) {
  .page { border-inline: 1px solid var(--nox-border); }
}
```

- [ ] **Step 2: Copy the used rules from `landing-shared.css`**

Copy verbatim these blocks (by section comment): nav (lines 25–47), buttons (49–62), stars (102–106), sections `.sec/.sec-label/.sec-title` (108–121), `.soon-card` + `.chip-avail/.chip-soon` (160–173), install steps (175–187), terminal block (189–199), footer (201–212).
**Drop:** `.frame`, `.frame-label` (canvas), `.hero` v1 block (64–100), `.feat-grid/.fcard` (124–134), `.shot-wrap/.shot/.shot-cap`→ keep only `.shot-cap` (142–146, it's used), `.dl-hero/.dl-card` (148–159), `.soon-row` (167), `.wm` (214–216), `.sec-sub`/`.rule-g` (unused on this page).

- [ ] **Step 3: Copy the used rules from `landing-v2.css`**

Copy verbatim lines 1–279 (lucide sizing, `.v2n` nav/footer, bands, `.hero2`, `.btn-glow`, `.flist`, phone mock, `.shot-glow`, `.dl2-*`, `.soon2`) — these all apply unchanged because `<body class="v2n">`.
**Drop:** lines 280–314 (`.mob2` block — folded into media queries in Step 5), lines 316–346 (`.hero-frame`, `.heroB`, `.heroC`).

- [ ] **Step 4: Classes replacing the design's layout-bearing inline styles (new code)**

```css
/* layout classes (were inline styles on the canvas frames) */
.sec-why  { padding-top: 80px; }
.sec-dl   { padding-top: 72px; padding-bottom: 80px; }
.why-grid { display: grid; grid-template-columns: 1fr auto; gap: 80px; align-items: center; }
.dl-grid  { display: grid; grid-template-columns: 1.25fr 1fr; gap: var(--sp-5); align-items: stretch; }
.dl2-side .soon2 { grid-template-columns: 1fr 1fr; }
.howto-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 48px; margin-top: 56px; align-items: start; }
.howto-grid .sec-title { font-size: 18px; }
.howto-grid .steps { margin-top: 20px; }
.howto-grid .term { margin-top: 20px; }
```

Keep the design's *small cosmetic* inline styles (star `left/top`, `shot-cap` margin) as-is in the HTML — they are per-instance data, not layout system.

- [ ] **Step 5: Responsive media queries (new code — values from the `.mob2` block, prefix dropped)**

```css
/* ---------- responsive ---------- */
@media (max-width: 960px) {
  .why-grid { grid-template-columns: 1fr; gap: 48px; }
  .why-grid .shot-glow { justify-self: center; margin-top: 8px; }
  .dl-grid { grid-template-columns: 1fr; }
  .howto-grid { grid-template-columns: 1fr; gap: 44px; margin-top: 44px; }
  .flist .fitem .fnum { display: none; }
}
@media (max-width: 700px) {
  .lp-nav { padding: 14px 20px; gap: var(--sp-4); }
  .lp-nav .brand span { font-size: 14px; }
  .hero2 { padding: 56px 24px 68px; }
  .hero2 .stage { width: 190px; height: 190px; }
  .hero2 .stage .mk { width: 112px; height: 112px; }
  .hero2 .orbit.o2 { inset: 18px; }
  .hero2 .word { font-size: 23px; margin-top: -28px; }
  .hero2 .hmeta { font-size: 9px; }
  .hero2 h1 { font-size: 34px; margin-top: 30px; }
  .hero2 .sub { font-size: 15px; margin-top: 18px; }
  .hero2 .cta-row { flex-direction: column; align-items: stretch; margin-top: 32px; gap: 12px; }
  .hero2 .btn { padding: 15px 24px; }
  .hero2 .plat { flex-direction: column; gap: 8px; margin-top: 26px; }
  .hero2 .plat .sep { display: none; }
  .sec { padding: 48px 24px; }
  .sec-why { padding-top: 56px; }
  .sec-dl { padding-bottom: 56px; }
  .flist .fitem { grid-template-columns: 48px 1fr; padding: 28px 2px; }
  .flist .fitem .ft { font-size: 16px; }
  .flist .fitem .fd { font-size: 14px; }
  .phone { width: 262px; height: 540px; }
  .dl2-android .inner { padding: 26px 22px; }
  .dl2-android .chip-avail { position: static; display: inline-block; margin-bottom: 16px; }
  .dl2-android .actions .btn { width: 100%; }
  .dl2-web { flex-wrap: wrap; }
  .dl2-web .btn { margin-left: 0; width: 100%; }
  .p-item .pv { max-width: 110px; }
  .foot { padding: 28px 24px; flex-direction: column; align-items: flex-start; gap: 12px; }
  .foot .sep { display: none; }
  .foot .right { margin-left: 0; flex-direction: column; align-items: flex-start; gap: 12px; }
}
```

- [ ] **Step 6: CSS-only burger nav (new code)**

```css
/* ---------- burger nav (CSS-only, <=700px) ---------- */
.nav-toggle { display: none; }
.burger {
  display: none; font-family: var(--font-mono); font-size: 18px;
  color: var(--nox-text-2); margin-left: auto; cursor: pointer; user-select: none;
}
@media (max-width: 700px) {
  .lp-nav { flex-wrap: wrap; }
  .burger { display: block; }
  .lp-nav .nav-right { display: none; }
  .lp-nav .links { display: none; }
  .nav-toggle:checked ~ .links {
    display: flex; flex-direction: column; gap: var(--sp-4);
    flex-basis: 100%; margin: 14px 0 4px; padding-top: 14px;
    border-top: 1px solid var(--nox-border);
  }
}
```

(`.burger` is a `<label for="nav-toggle">` placed after `.nav-right`; the checkbox sits before `.links` so `~` works.)

- [ ] **Step 7: Stub link marker (new code)**

```css
/* pending destinations — tracked in README "Pending links" */
[data-stub] { cursor: default; }
```

- [ ] **Step 8: Verify + commit**

Run: `grep -nE "\.frame|\.mob2|\.heroB|\.heroC|http" css/style.css`
Expected: no output.

```bash
git add css/style.css
git commit -m "Add page stylesheet: merged design CSS, responsive queries, burger nav"
```

---

### Task 5: `index.html`

**Files:**
- Create: `index.html`
- Source markup: design file lines 23–204 (nav→footer inside the v2 frame)

- [ ] **Step 1: Fetch Lucide SVGs to inline (working dir, not committed)**

```bash
mkdir -p arcan-extracted/icons
nix-shell -p curl --run '
  for i in list download globe lock cloud-off settings message-square users plus smartphone terminal monitor laptop tablet mail; do
    curl -sL "https://unpkg.com/lucide-static@0.525.0/icons/$i.svg" -o "arcan-extracted/icons/$i.svg";
  done'
grep -L "svg" arcan-extracted/icons/*.svg || echo ALL-OK
```

Expected: `ALL-OK`. Each file is a 24×24 `stroke="currentColor"` SVG.

- [ ] **Step 2: Write `index.html`**

Head (exact):

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Arcan — the messenger with nothing to leak</title>
<meta name="description" content="Arcan is a local-first, end-to-end encrypted messenger. Keys stay on your device, history stays yours, and everything works offline — no phone number, no analytics." />
<meta property="og:title" content="Arcan — the messenger with nothing to leak" />
<meta property="og:description" content="Local-first, end-to-end encrypted messenger. Free and open source." />
<meta name="theme-color" content="#0b0d14" />
<link rel="icon" href="favicon.ico" sizes="48x48" />
<link rel="icon" type="image/png" sizes="32x32" href="favicon-32.png" />
<link rel="icon" type="image/png" sizes="16x16" href="favicon-16.png" />
<link rel="apple-touch-icon" href="apple-touch-icon-180.png" />
<link rel="stylesheet" href="css/fonts.css" />
<link rel="stylesheet" href="css/tokens.css" />
<link rel="stylesheet" href="css/style.css" />
</head>
<body class="v2n">
<div class="page">
```

Body: copy design lines 23–204 in order, then `</div></body></html>`. Apply these exact substitutions while copying:

| Design | Production |
|---|---|
| `<div class="frame v2n" ...>` wrapper + `frame-label` spans | dropped (replaced by `.page` div); all `data-screen-label` attrs dropped |
| `<i data-lucide="NAME"></i>` | inline SVG from `arcan-extracted/icons/NAME.svg`: root tag rewritten to `<svg class="lucide" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">` keeping inner paths verbatim |
| nav `<div class="links">` | prepend `<input type="checkbox" id="nav-toggle" class="nav-toggle" aria-hidden="true" />` before it; append `<label for="nav-toggle" class="burger" aria-label="menu">≡</label>` after `.nav-right` |
| nav link `href="#"` features / download | `href="#why"` / `href="#download"`; add `id="why"` to the why-section, `id="download"` to the download section |
| all `github` links (nav, feature 02 `github ↗`, footer) `href="#"` | `href="https://github.com/nox-decima/arcan"` (verified/corrected in Task 8 Step 2) |
| footer `nox-decima.dev` `href="#"` | `href="https://nox-decima.dev"` |
| nav "open in browser" + "download" buttons, hero CTAs, `download .apk`, `sha256 checksums ↗`, browser card `open →` | keep `href="#"`, add `data-stub="web-app"` / `data-stub="apk-download"` / `data-stub="checksums"` respectively |
| `<section class="sec band-void" style="padding-top: 80px;">` | `<section class="sec band-void sec-why" id="why">` |
| why-section inner `<div style="display: grid; grid-template-columns: 1fr auto; gap: 80px; align-items: center;">` | `<div class="why-grid">` |
| `<section class="sec band-panel" style="padding-top: 72px; padding-bottom: 80px;">` | `<section class="sec band-panel sec-dl" id="download">` |
| download grid `<div style="display: grid; grid-template-columns: 1.25fr 1fr; ...">` | `<div class="dl-grid">` |
| `<div class="dl2-android" style="height: 261px;">` | `<div class="dl2-android">` (canvas height-equalizer dropped) |
| `<div class="soon2" style="grid-template-columns: 1fr 1fr;">` | `<div class="soon2">` (handled by `.dl2-side .soon2` rule) |
| install/verify `<div style="display: grid; grid-template-columns: 1fr 1fr; gap: 48px; margin-top: 56px; align-items: start;">` | `<div class="howto-grid">` |
| its two `<div class="sec-title" style="font-size: 18px;">` + `<ol class="steps" style="margin-top: 20px;">` + `<div class="term" style="margin-top: 20px;">` | same elements without inline styles (covered by `.howto-grid` rules) |
| `<script src="https://unpkg.com/lucide@latest"></script>` + `<script>lucide.createIcons();</script>` | dropped entirely |

Keep verbatim: star spans (with their inline positions), hero stage/orbits, all copy, phone mockup, `brand/svg/mark-full-white.svg` src → `assets/mark-full-white.svg` (5 occurrences), mailto link, jazz.tools link.

- [ ] **Step 3: Verify**

```bash
grep -c "data-lucide" index.html   # expect 0
grep -nE "unpkg|googleapis|gstatic|<script" index.html   # expect no output
grep -c "data-stub" index.html     # expect 7 (2 nav, 2 hero, 2 android card, 1 browser card... adjust: count must equal the # of stub anchors)
```

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Add landing page: v2 design in document flow, inlined icons, stub-marked links"
```

---

### Task 6: Local verification

- [ ] **Step 1: Serve and smoke-test**

```bash
python3 -m http.server 8931 --bind 127.0.0.1 &
sleep 1 && curl -s http://127.0.0.1:8931/ | head -5
```

Expected: the doctype + `<title>Arcan — the messenger with nothing to leak</title>`.

- [ ] **Step 2: Screenshot at 1440 and 390, compare to design**

```bash
nix-shell -p chromium --run '
  chromium --headless --disable-gpu --hide-scrollbars --window-size=1440,3200 --screenshot=/tmp/arcan-1440.png http://127.0.0.1:8931/ &&
  chromium --headless --disable-gpu --hide-scrollbars --window-size=390,3400 --screenshot=/tmp/arcan-390.png http://127.0.0.1:8931/'
```

Read both PNGs and compare against `arcan-extracted/screenshots/v2-list.png` and the design frames: banded backgrounds, orbit hero, feature list + phone, gradient-border android card, terminal block, footer gradient rule. Fix any visual regressions before proceeding.

- [ ] **Step 3: Zero-third-party audit**

```bash
grep -rhoE "https?://[^\"' )]+" index.html css/ | sort -u
```

Expected output — exactly these three, all plain `<a href>` destinations (nothing loaded):
`https://github.com/nox-decima/arcan` (or corrected URL), `https://jazz.tools`, `https://nox-decima.dev`.

- [ ] **Step 4: Kill server, commit any fixes**

```bash
kill %1
git add -A && git diff --cached --quiet || git commit -m "Visual fixes from local verification"
```

---

### Task 7: README with pending-links table

**Files:**
- Create: `README.md`

- [ ] **Step 1: Write `README.md`**

```markdown
# arcan-website

Static landing page for [Arcan](https://github.com/nox-decima/arcan) — a local-first,
end-to-end encrypted messenger. Built from the "Arcan Landing Explorations v2" design.

- No build step: GitHub Pages serves the repo root (`main` branch).
- No JavaScript, no third-party requests: fonts and icons are committed to the repo.
- Live: https://OWNER.github.io/arcan-website/

## Editing

`index.html` is the whole page. Styles: `css/tokens.css` (design tokens),
`css/style.css` (page), `css/fonts.css` (@font-face). Local preview:
`python3 -m http.server` and open http://localhost:8000.

## Pending links

These anchors are stubs (`href="#"`, marked `data-stub` in the HTML) until the
destinations exist:

| data-stub | Where | Waiting on |
|---|---|---|
| `web-app` | nav, hero, download section ("open in browser") | Arcan web client URL |
| `apk-download` | nav, hero, android card ("download") | first public APK release |
| `checksums` | android card ("sha256 checksums") | published release checksums |
```

(`OWNER` replaced with the real GitHub username in Task 8 Step 2.)

- [ ] **Step 2: Register the three stubs as follow-ups**

Session action (followup-tracking skill): create TaskList items with `metadata.kind = "followup"` for wiring `web-app`, `apk-download`, and `checksums` links when their destinations exist; also append them to `~/claude-followups/2026-07-12.md`.

- [ ] **Step 3: Commit**

```bash
git add README.md
git commit -m "Add README with pending-links table"
```

---

### Task 8: GitHub repo + Pages deployment

- [ ] **Step 1: Check gh auth**

```bash
nix-shell -p gh --run 'gh auth status'
```

Expected: logged in. **If not:** stop and ask the user to run `! nix-shell -p gh --run "gh auth login"` in the session, then re-run.

- [ ] **Step 2: Resolve the real Arcan repo URL + owner**

```bash
nix-shell -p gh --run 'gh api user --jq .login && gh repo list --limit 100 --json name,owner --jq ".[] | select(.name | test(\"arcan\"; \"i\")) | .owner.login + \"/\" + .name"'
```

If the Arcan app repo differs from `nox-decima/arcan`, or the login differs from `OWNER` in README: `sed -i` the URLs in `index.html` and `README.md`, then `git add -A && git commit -m "Point links at real GitHub owner/repo"`. If no Arcan app repo exists at all, downgrade the three github links to `data-stub="app-repo"` stubs, update README table, commit, and register a follow-up.

- [ ] **Step 3: Create public repo and push**

```bash
nix-shell -p gh --run 'gh repo create arcan-website --public --source . --push --description "Arcan landing page — local-first, E2EE messenger"'
```

Expected: repo created, `main` pushed.

- [ ] **Step 4: Enable GitHub Pages (main / root)**

```bash
nix-shell -p gh --run 'gh api -X POST "repos/{owner}/arcan-website/pages" -f "source[branch]=main" -f "source[path]=/" 2>/dev/null || gh api -X PUT "repos/{owner}/arcan-website/pages" -f "source[branch]=main" -f "source[path]=/"'
```

- [ ] **Step 5: Verify live**

```bash
sleep 90 && curl -sI "https://$(nix-shell -p gh --run 'gh api user --jq .login').github.io/arcan-website/" | head -3
```

Expected: `HTTP/2 200` (retry after another minute if 404 — first Pages build can lag). Then fetch the body and confirm the `<title>`. Report the live URL to the user.

---

## Self-review notes

- Spec coverage: repo layout (T1–T3,7), page structure + substitutions (T5), responsive + burger (T4), assets (T1–T2, T5S1), links & stubs (T5, T7, T8S2), deployment (T8), verification (T6, T8S5). Out-of-scope items untouched.
- The `data-stub` count check in T5S3 is intentionally re-derived while writing the HTML (nav 2 + hero 2 + android 2 + browser 1 = 7).
- Type/name consistency: `nav-toggle`, `.burger`, `.why-grid`, `.dl-grid`, `.howto-grid`, `.sec-why`, `.sec-dl`, stub keys `web-app`/`apk-download`/`checksums` used identically across T4/T5/T7.
