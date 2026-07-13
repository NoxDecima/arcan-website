# arcan-website

Static landing page for [Arcan](https://github.com/NoxDecima/arcan) — a local-first,
end-to-end encrypted messenger. Built from the "Arcan Landing Explorations v2" design.

- No build step: GitHub Pages serves the repo root (`main` branch).
- No JavaScript, no third-party requests: fonts and icons are committed to the repo.
- Live: https://arcan.nox-decima.dev/

## Editing

`index.html` is the whole page. Styles: `css/tokens.css` (design tokens),
`css/style.css` (page), `css/fonts.css` (@font-face). Local preview:
`python3 -m http.server` and open http://localhost:8000.

## Release links

The download buttons point at GitHub's permanent latest-release redirect:

    https://github.com/NoxDecima/arcan/releases/latest/download/app-universal-release.apk

This always serves the newest release **as long as each release uploads its APK
under the same asset name** (`app-universal-release.apk`). If the asset is ever
renamed, update the three download links in `index.html` to match.

The "sha256 checksums" link goes to the latest release page, where GitHub shows
each asset's digest. "open in browser" links go to https://app.arcan.nox-decima.dev.
