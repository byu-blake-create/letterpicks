# Letterpicks

Sister site to [Letterstats](https://letterstats.ndblake.com). Drop in one, two, or three Letterboxd export ZIPs and Letterpicks recommends what you should watch next.

Live: https://letterpicks.ndblake.com

## Modes

- `1 person`: recommends movies you'd probably like based on your highest-rated films, your watchlist, and a small set of unseen TMDB-similar candidates.
- `2 people`: keeps the existing shared-watchlist ranking, suggests movies neither of you has seen yet, and adds a favorites-handoff list for movies one person loved that the other has not seen.
- `3 people`: same as 2-person mode, but optimized for three-way consensus.

## How it works

1. Export your Letterboxd data from `Settings → Data → Export Your Data`.
2. Drop one `.zip` into each active slot.
3. Letterpicks parses everything in the browser. TMDB lookups also happen client-side using the baked-in read-only token.

### Shared-watchlist picks

For movies already in every uploaded watchlist:

- Letterpicks looks up the movie on TMDB.
- It fetches TMDB's curated `similar` list for that title.
- For each user, it finds which similar movies they have rated and turns that into a predicted score.
- Each prediction is shrunk toward that user's overall average rating so sparse overlap does not swing too hard.
- The group rank uses the harmonic mean of the per-user predictions, then applies an agreement penalty when one person's predicted score is much lower than the others.

### Not-in-common group picks

For 2-person and 3-person mode, Letterpicks also builds a second pool:

- First choice: movies from the union of all uploaded watchlists that nobody in the group has already watched or rated.
- Fallback: a small number of unseen TMDB-similar titles derived from each user's highest-rated films.
- Movies already watched or rated by any uploaded user are filtered out when that data is available.
- Ranking uses the same per-user prediction logic, plus a small bonus for union-watchlist movies so the output stays grounded in what someone already saved.

### Favorites handoff picks

In 2-person mode, Letterpicks also builds a third list:

- It finds movies one person rated highly.
- It filters out anything the other person has already watched or rated.
- It predicts how much the unseen person would like that movie using the same similar-movie model.
- It ranks higher when the person who has seen it rated it strongly and the unseen person's predicted score is strong.

### Single-user picks

For 1-person mode:

- Letterpicks scores unseen movies from your watchlist first.
- If your watchlist is thin, it adds unseen TMDB-similar titles based on movies you rated highly.
- Each candidate gets a predicted score from similar movies you've rated, with your overall average as the fallback when overlap is weak.

## Stack

- Static HTML + vanilla JS, single file: `index.html`
- Browser libs from CDN: `jszip` for ZIP parsing, `papaparse` for CSV parsing
- TMDB API for movie metadata and similarity
- No backend, no database, no persistence beyond browser localStorage for theme + TMDB cache

## Local dev

```bash
cd /home/ubuntu/projects/letterpicks
python3 -m http.server 8000
# visit http://localhost:8000
```

## Deploy

Deploy as a static site. The same pattern as Letterstats works fine: any static host serving `index.html` behind your preferred domain routing.

## TMDB API

Letterpicks ships with a baked-in TMDB v4 read-only token. Typical sessions stay comfortably below TMDB's free-tier rate limits because responses are cached in localStorage and requests are throttled client-side.

## Privacy

- No server, no database. Everything happens in the browser tab.
- Refresh the page and uploaded ZIPs plus computed picks are gone.
- TMDB response cache lives in localStorage. Clear site data to wipe it.
