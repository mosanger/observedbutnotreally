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

## Analytics (Google Analytics 4)

The GA4 gtag snippet is already in `index.html`'s `<head>` with a placeholder ID
(`G-XXXXXXXXXX`) — the site is wired up but tracks nothing until you activate it:

1. Create/find your property at <https://analytics.google.com> → Admin → Data Streams → Web.
2. Copy the **Measurement ID** (format `G-XXXXXXXXXX`).
3. In `index.html`, replace **both** occurrences of `G-XXXXXXXXXX` (the `gtag/js?id=` URL and the `gtag('config', …)` call).
4. Commit + push. Page views appear in Analytics within ~24h.

Note: this is a bare page_view setup — no consent banner, no custom events. If you ever
want scroll/spread-view events or EU consent handling, say the word.

## Current state (2026-08-20)

- 25 spreads, metadata embedded and matching the fallback text.
- `[Name unknown]` (the checkered-hat hero) is **not** in the export set — export it
  as a numbered image if you want that spread back.
- `image-15` (Liberty): the embedded title is typed `LIberty` (capital I) — fix in
  Lightroom and re-export, or edit the XMP directly; the site will show whatever
  the metadata says.
- Cover (`images/cover.jpg`) is hardcoded — its title is the book title, not metadata.
- Copyright is embedded on every export ("Markus Osanger").
