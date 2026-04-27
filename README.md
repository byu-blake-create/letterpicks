# Letterpicks

Sister site to [Letterstats](https://letterstats.ndblake.com). Drop in two Letterboxd export ZIPs and Letterpicks finds the films in **both** your watchlists, then ranks which ones you'd both enjoy most.

Live: https://letterpicks.ndblake.com

## How it works

1. You and a partner each export your Letterboxd data: `Settings → Data → Export Your Data`. You'll each get a `.zip`.
2. Drop both ZIPs into Letterpicks (one per slot).
3. Paste a free TMDB API key (themoviedb.org → Settings → API).
4. Letterpicks parses both ZIPs **entirely in your browser** — nothing is uploaded.

For each film in your shared watchlist:

- It looks up the film on TMDB.
- It pulls TMDB's curated list of "similar" films (`/movie/{id}/similar`).
- For each of you, it checks which of those similar films you've already rated, then averages those ratings into a predicted score for the candidate.
- The combined ranking score is the **harmonic mean** of your two predicted scores — that way a candidate one of you would clearly dislike doesn't rank high, even if the other person would love it.
- If there's no rating overlap, it falls back to each user's overall average.

## Stack

- Static HTML + vanilla JS, single file (`index.html`).
- Browser libs from CDN: `jszip` (ZIP parsing), `papaparse` (CSV parsing).
- TMDB API for movie metadata + similarity.
- No backend, no database, no persistence beyond your browser's localStorage (just the API key + a TMDB response cache so re-runs are fast).

## Local dev

It's a single HTML file. Open it in a browser:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deploy

Deploy as a static site any way you like. The same pattern as letterstats works — point a Cloudflare-tunnel-routed domain at a static host (Coolify static service, GitHub Pages, Cloudflare Pages, etc.) serving `index.html`.

## TMDB API key

A free TMDB v3 API key is required. Get one at https://www.themoviedb.org/settings/api. The key is stored only in your browser's localStorage; it never leaves your machine except in calls direct to TMDB.

## Privacy

- No server, no database. Everything happens in the browser tab.
- Refresh the page and your uploaded ZIPs and computed picks are gone.
- The TMDB response cache and API key live in localStorage; clear site data to wipe them.
