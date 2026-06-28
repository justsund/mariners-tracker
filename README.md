# Did the Mariners Score More Than 3?

A single-page dashboard that answers one question about the Seattle Mariners: did they score more than 3 runs? Live game state, season tally, a season trend line, and the last 10 games. Built as one self-contained HTML file, no build step, no dependencies.

Live: https://didthemarinersscoremorethan3.com

## Files

| File | What it is | Deployed? |
| --- | --- | --- |
| `index.html` | The file GitHub Pages serves. Self-contained (HTML + CSS + JS inline). | Yes |
| `mariners.html` | Working source. Kept byte-identical to `index.html`. Edit here first. | No |
| `mariners-preview.html` | QA harness: same page plus a mock Live / Today / Final state switcher (intercepts fetch with fake data). Use it to test states without waiting for a real game. | No |
| `combos-demo.html` | One-off explainer of the color logic (win/loss vs over/under). | No |
| `qa-stress.js` | Headless Puppeteer regression test. Run with `node qa-stress.js` (needs puppeteer in `C:\AIMaker\node_modules`). | No |

## Data source

Everything comes from the public MLB Stats API. No key, no auth, and it is CORS-enabled, which is why the browser can call it directly from a static page.

- Schedule: `https://statsapi.mlb.com/api/v1/schedule?sportId=1&teamId=136&season=YYYY&gameType=R`
- Live feed: `https://statsapi.mlb.com/api/v1.1/game/{gamePk}/feed/live`
- Team id 136 = Seattle Mariners. The API has historical data going back decades (useful for the parked multi-season idea below). Do not use Baseball Reference: no public API, scraping is against their terms, and CORS would block it anyway.

## How to update

1. Edit `mariners.html` (mirror the same change into `mariners-preview.html` so QA stays accurate).
2. Copy it over the deployed file: `Copy-Item mariners.html index.html -Force`.
3. Push from the personal `justsund` account. The simplest path is the GitHub web UI: repo, Add file, Upload files, drag `index.html`, commit to `main`.
4. GitHub Pages redeploys in about a minute. Hard-refresh the site (Ctrl+F5). The `CNAME` file owns the custom domain, leave it alone.

Note: the Copilot CLI account (`justinsund_microsoft`) only has read access to this repo, so commits and the deploy have to come from `justsund`.

## Conventions (so future me stays consistent)

- No emojis in content and no em dashes. They read as AI-generated. The only emojis are the trident and cloud on the design toggle buttons (deliberate UI).
- Classic is always the default landing design. Springfield is opt-in per visit and is not persisted.
- Color logic:
  - Game score: blue/teal = win, red = loss.
  - Verdict pill (the run question): blue/teal YES = scored more than 3, red NO = 3 or fewer.
  - Row dots are a neutral yellow (the outcome depends on two independent things, so the dot picks neither).
  - Status badges (Live, Today, Final) are yellow (they signal "active," not good/bad).
  - Neutral percentage facts (season tally percent, "X 3 or fewer," the season trend line) are yellow.
- Springfield can't use yellow text on its white cards, so it substitutes its own palette (Marge blue for the trend line, black for neutral stats).

## Parked / future ideas

- Multi-season comparison (2001 to now): loop the schedule endpoint per season, compute the percent of games scoring 3 or fewer, and compare across years. Lazy-load it so it does not slow the main page.
