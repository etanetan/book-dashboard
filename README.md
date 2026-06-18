# Book Bot Dashboard

A static web dashboard that visualizes my personal reading data — the books I
own, the daily recommendations a "Book Bot" sends me, and my feedback on them.
It's a single HTML page that renders a JSON data file, deployed via GitHub Pages.

## Live dashboard

**https://etanetan.github.io/book-dashboard/**

> The repository is private, so the page is served with *private* GitHub Pages
> visibility — it only loads when you're signed into GitHub with an account that
> has access. To make it publicly viewable, set the repo to public (or
> **Settings → Pages → Visibility → Public**).

## What it shows

- **Stats** — books owned, recs sent, days running, loved, passed.
- **Genres recommended** — a breakdown of recommendation counts by genre.
- **👍 Loved / 👎 Not for me** — books reacted to (feedback loop).
- **Recommendation history** — the daily picks, tagged by genre, with 🎲
  wildcard picks highlighted.
- **Your library** — the full owned-books list with live search.

## How it works

The project is intentionally just two files:

| File | Purpose |
| --- | --- |
| `index.html` | The dashboard UI. Plain HTML/CSS/JS — no build step, no dependencies. On load it `fetch`es `dashboard.json` (cache-busted) and renders it. |
| `dashboard.json` | The data the page renders. Refreshed regularly and committed to `main`. |

### `dashboard.json` schema

```jsonc
{
  "updated": "2026-06-17T13:11:07+00:00",  // ISO timestamp of last refresh
  "library": [                              // owned books, "Title by Author.epub"
    "1984 by George Orwell.epub"
  ],
  "rec_history": [                          // newest day first
    {
      "date": "2026-06-17",
      "picks": [
        { "title": "The Quantum Thief", "genre": "sci-fi" },
        { "title": "Educated", "genre": "wildcard" }
      ]
    }
  ],
  "feedback": {                             // reactions to past recs
    "loved": [],
    "disliked": []
  },
  "stats": {
    "owned_count": 397,
    "recs_count": 262,
    "rec_days": 25,
    "loved_count": 0,
    "disliked_count": 0,
    "by_genre": { "fantasy": 36, "sci-fi": 22, "literary": 6 }
  }
}
```

Notes:
- A pick with `"genre": "wildcard"` renders as a 🎲 wildcard instead of a genre tag.
- `library` entries are shown with the trailing `.epub` stripped.

## Data refresh

`dashboard.json` is regenerated periodically (roughly daily) from my
Goodreads + Drive library and pushed to `main` as `dashboard: refresh <timestamp>`
commits. That generator currently lives **outside this repo**, so this repo holds
the dashboard and its data, but not (yet) the pipeline that produces the data.

## Local development

No build or server is strictly required, but `fetch` needs HTTP (not `file://`),
so serve the folder:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Deployment

Published automatically by GitHub Pages (`pages-build-deployment`) on push to the
published branch — no manual deploy step.
