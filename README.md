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
