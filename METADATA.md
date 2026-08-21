# Observedbutnotreally — metadata pipeline (local images)

The site reads each spread's **title** and **caption** from the picture files themselves:
- `h2` (character name) ← XMP `dc:title` embedded in the JPEG
- caption `<p>` ← XMP `dc:description` embedded in the JPEG

Reading is done in the browser with ExifReader (vendored at `js/exifreader.min.js`,
MPL-2.0, https://github.com/mattiasw/ExifReader). No API keys, no Flickr, no network
dependency — it works offline and on GitHub Pages as-is.

The text hardcoded in `index.html` is only a **fallback** (shown if JS is off or the
file can't be parsed). The embedded metadata is the source of truth and wins on load.

## Authoring workflow (Lightroom Classic)

The images in `images/` are Lightroom exports that already carry the metadata —
that's the pipeline:

1. In Lightroom, set **Title** = character name, **Caption** = caption text.
2. Export to `images/` with **Include Metadata: All Except Camera Raw** (checked).
   Use the same filename as the spread you're replacing (e.g. `image-02.jpg`).
3. `git commit` the new file — the site text updates automatically.

Roughly the same flow works from any editor that writes XMP `dc:title`/`dc:description`
(e.g. exiftool: `exiftool -XMP-dc:Title="Marla" -XMP-dc:Description="..." image.jpg`).

## Order & filenames

- `image-NN.jpg` numbering **is the book order**: `image-01` … `image-25`.
- To reorder spreads: rename the files (and the `<section>` order in `index.html`).
- To add a spread: export as `image-26.jpg`, copy one `<section class="portfolio-page">`
  block in `index.html`, swap the `src`. The left/right alternation is automatic —
  CSS `:nth-child(even/odd)` handles it; no class changes needed.

## Analytics (Umami Cloud)

Cookieless, privacy-first analytics — **no consent banner, no cookies, no
localStorage**. Replaces the former GA4 + consent-banner setup (removed 2026-08-21).

- Snippet in `index.html`'s `<head>`:
  `<script defer src="https://cloud.umami.is/script.js" data-website-id="33dd6593-2199-4fd5-bb9a-765e5a76b094"></script>`
- Page views are tracked automatically by the script.
- `spread_view` custom event via `window.umami?.track(...)` (params: `spread`
  character name, `spread_index` 1-based, `image` filename) — fired once per
  spread at ≥50% visibility (IntersectionObserver).
- Free Hobby plan: 100k events/month, 6-month retention, 1 website. Dashboard at
  umami.is. If the free tier's event budget ever becomes a problem, self-hosting
  is an option (MIT license).

**Imprint & Privacy:** on its own page at `/imprint/` (`imprint/index.html`,
English, address Bürgerspitalgasse 22/35, 1060 Wien), linked from the site footer.
Update it if you move or change contact info.

## Current state (2026-08-21)

- **30 spreads** (was 25): re-sequenced 2026-08-21 — five new characters (Walter,
  Rafael & Rafael, Lena, Antonia, Fabian & Kerstin); several files renumbered and
  captions updated (Liberty, Jessy, Marla, Hans…). The HTML fallback text is
  byte-identical to the embedded metadata for all 30 files.
- Favicon: the cover photo (crowd behind the barrier, square crop) — PNG sizes
  16/32/192/512 + apple-touch-icon (180).
- `[Name unknown]` (the checkered-hat hero) is **not** in the export set — export it
  as a numbered image if you want that spread back.
- Cover (`images/cover.jpg`) is hardcoded — its title is the book title, not metadata.
- Copyright is embedded on every export ("Markus Osanger").
