# Observedbutnotreally — Flickr metadata pipeline

The site reads each spread's **title** and **caption** live from Flickr photo metadata
via the Flickr API (`flickr.photos.getInfo`). The HTML text you see in the source is
only a **fallback** shown until the API key is set, or if Flickr is unreachable.

- `h2` (character name) ← Flickr **title**
- caption `<p>` ← Flickr **description**

## 1. Get an API key (5 minutes)

1. Go to <https://www.flickr.com/services/apps/create/> → "Request an API key"
2. Pick **non-commercial**, fill in the short form (app name, e.g. "Observations of an Absent Mind site")
3. Paste the key into `index.html`:

```js
const FLICKR_API_KEY = 'your-key-here';
```

That's the only code change ever needed for this. The key is read-only and only ever
fetches *public* photo data, so it is safe to ship in the page.

## 2. Authoring workflow (Lightroom Classic)

The clean path: edit metadata in Lightroom, publish to Flickr, done.

- Use the built-in **Publish Services → Flickr** (or any Flickr upload).
- Lightroom's **Title** field (IPTC) → Flickr title → becomes the `h2`
- Lightroom's **Caption** field (IPTC) → Flickr description → becomes the caption

To update a spread later: edit Title/Caption in Lightroom → right-click the photos
in the Publish service → **Publish Now**. If the built-in service doesn't push changed
metadata reliably (known quirk in some versions), the Jeffrey Friedl Flickr plugin
(<https://regex.info/blog/lightroom-goodies/flickr>) syncs it properly.

Rules:
- **Title = character name only.** No caption sentences in the title field.
- **Caption = plain text.** No HTML, no links. Newlines are fine (they render as breaks).
- The cover photo does **not** need any metadata — its title is hardcoded.

## 3. Re-org checklist (one-time, 13 photos)

| Photo ID | Character | Title (set this) | Description (set this) |
|---|---|---|---|
| 237949527 | Marla | Marla | sometimes, marla throws biodegradable garbage out the window. |
| 237949826 | Hans | Hans | i can't remember what's special about hans. he's a quiet one. |
| 276513132 | Jessy | Jessy | who's like totally freaked out about the guy with the camera behind her back. |
| 298356238 | Unnamed object | Unnamed object | a sick-of-shopping shopping cart. |
| 454672129 | Gerti | Gerti | still undefeated in the category best hair of the year. meticulous as usual. |
| 255865219 | Helmut and Roberta | Helmut and Roberta | helmut, who doesn't think about his dummy anymore and roberta who's still in love with that highschool-boy. |
| 221277779 | Liberty | Liberty | practicing her oscar acceptance speech once again as the world around her freezes in time. |
| 2022161605 | Reinhard | Reinhard | still unsure if reinhard and pumuckl are laughing about the same thing but the thought of it calms me. |
| 2639257057 | Debbie | Debbie | hmm. a crochet circle? with paula?! those 5 minutes aftert church are already hell on earth. |
| 2675125415 | Bob and Bob jr | Bob and Bob jr | sadly, one day little bob will lose his love for balloons and will immediately look exactly like old bob. |
| 2675127857 | [Name unknown] | [Name unknown] | she's probably still unaware but these are the heroes we need, undefinable, in their own mysterious box, imaginably capable, and ever evolving. lesbian (or lesbian reading - both cool), femme, butch, ready to touch down and document their success. plus, practically immune against a sun-stroke. name not known as of printing, any tips are highly appreciated. |
| 54581831523 | Tatjana | Tatjana | she could have definitely chosen a more optimal location for her refreshment stand but she like it quiet and gray. theres's on average one lost tourist and a few guys in suits everyday. And those pay really well if you start trauma dumping. |
| 2099531092 | Markus | Markus | observing. |

Current Flickr state (as of 2026-08-20), so you can spot what's off:

- Most captions currently live in the **title** slot — they move to the description.
- **Gerti**: title = "and the price for the best hair this evening goes to...", description = "gerti." → consolidate into title "Gerti" + description above.
- **Helmut and Roberta**: description currently contains an old HTML link — remove it, keep plain text.
- **Liberty**: title = "roadway star" → replace with the character name. (The HTML used a longer caption than your newest draft — settle on one; whatever sits in the description is what renders.)
- **Reinhard**: current Flickr title says "Randy and Pumuckl", the site said "reinhard and pumuckl" — decide.
- **Debbie**: current Flickr title is an older draft ("Hmm. Crochet? Sounds so up my couch…") — replace.
- **Markus**: title = "200710161_21" (original filename) → "Markus".

## 4. Adding a new spread

1. Copy one `<section class="portfolio-page" data-photo-id="…">` block, swap the image URL + photo ID + fallback text.
2. The left/right alternation is automatic — CSS `:nth-child(even/odd)` handles it; no class changes needed.
3. Set title/caption in Flickr (via Lightroom) and it renders automatically.
