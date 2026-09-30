# PickMe — AI Recommendation Engine

A simple, fully client-side **content-based recommendation system**. The user
describes what they like (categories, genres, moods, time commitment, era, and
optionally an item they already love); the engine scores every item in a
42-item catalog of movies, books and games and shows the best matches — each
with a plain-English explanation of *why* it was recommended.

**Key skills demonstrated:** logic building, pattern matching, set/vector
similarity, weighted scoring, recommendation concepts (content-based filtering,
item-to-item similarity, cold-start seeding).

---

## ✅ Completed features

| Feature | Details |
|---|---|
| Preference input | Chip-style multi-select for categories, 12 genres, 8 moods; radio chips for length & era; dropdown to pick a "seed" item you already love |
| Hard filtering | Category filter and "hide item I already know" exclusion applied before scoring |
| Set similarity | **Jaccard similarity** (|A∩B| / |A∪B|) blended 50/50 with **coverage** (|A∩B| / |A|) for both genres and moods |
| Weighted scoring | `score = 0.50·genre + 0.30·mood + 0.10·length + 0.05·era + 0.05·rating` |
| Graduated length match | Exact = 1.0, neighbouring length = 0.4, opposite = 0 |
| Item-to-item similarity | Items vectorised over a shared 25-feature vocabulary; **cosine similarity** powers "Because you liked …" and the per-card "More like this" button |
| Cold-start seeding | If a seed item is chosen but no genres/moods are ticked, the seed's tags are borrowed as the profile |
| Explanations | Every card lists the matched reasons plus a per-signal score-bar breakdown |
| "Surprise me" | Randomises 2 genres + 1–2 moods and runs the engine |
| Shareable URLs | Preferences are written to the query string and restored on load |
| Responsive UI | Dark modern theme, verified at desktop (1280px) and phone (390px) widths |
| Accessibility | Semantic sections, `fieldset`/`legend`, focus-visible rings on chips, `aria-live` results region |

---

## 🔗 Entry points

| Path | Purpose |
|---|---|
| `index.html` | The whole app (single page) |
| `index.html?genres=sci-fi,thriller&moods=dark,intense&seed=m1` | Deep link: pre-fills preferences and auto-runs recommendations |

Supported query parameters: `category` (comma list of `movie,book,game`),
`genres`, `moods` (comma lists), `length` (`short|medium|long`),
`era` (`classic|modern`), `seed` (item id such as `m1`, `b3`, `g7`).

Anchors: `#preferences-section`, `#results-section`, `#how-it-works`.

---

## 🗂 Project structure

```
index.html          – page markup, form, result-card <template>
css/style.css       – theme, chips, cards, score bars, responsive rules
js/data.js          – catalog (ITEMS) + tag vocabularies (GENRES, MOODS, LENGTHS, ERAS)
js/recommender.js   – the engine: jaccard(), coverage(), vectorize(), cosine(), recommend(), similarTo()
js/main.js          – UI glue: builds chips, reads form, renders cards, URL sync
```

## 🧠 Data model

Each catalog item (`js/data.js`) looks like:

```js
{ id: 'm1', category: 'movie', title: 'Inception', year: 2010,
  genres: ['sci-fi', 'thriller', 'action'],
  moods:  ['thought-provoking', 'intense'],
  length: 'long', era: 'modern', rating: 8.8, blurb: '…' }
```

Storage: **none required** — the catalog is a static JS array; all matching
runs in the browser. No backend, no tables, no API keys.

## ⚙️ How the matching logic works

1. **Filter** – drop items outside chosen categories / explicitly excluded.
2. **Compare sets** – for genres and moods compute
   `0.5 · jaccard(user, item) + 0.5 · coverage(user, item)`.
   Jaccard penalises items with many unrelated tags; coverage rewards items
   that hit everything the user asked for.
3. **Match scalars** – length (graduated), era (exact), rating (normalised).
4. **Weighted sum** – combine with weights 50/30/10/5/5 and sort descending.
5. **Item-to-item** – each item → binary vector over
   `genre:*`, `mood:*`, `length:*`, `era:*` features; cosine similarity ranks
   nearest neighbours (same story in another medium is skipped).

---

## 🚧 Not yet implemented

- Persisting a user's liked/disliked history (could use the RESTful Table API)
- Collaborative filtering (needs multiple users' ratings)
- Free-text interest input with keyword → tag mapping
- Adjustable weights UI (sliders) to let users tune the formula live

## 💡 Recommended next steps

1. Add a 👍/👎 feedback loop that adjusts the profile (simple online learning).
2. Store favourites via `tables/favourites` so they survive reloads.
3. Add a free-text box that maps synonyms ("space", "aliens") to `sci-fi`, etc.
4. Grow the catalog or load it from a JSON file / table.
5. Expose weight sliders in the "How it works" section to make the formula interactive.

---

## 🌐 Public URL

Not yet published. Use the **Publish tab** to deploy the site and obtain a live URL.
