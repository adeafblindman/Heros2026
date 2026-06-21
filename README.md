# Charity Ticker (OBS / GitHub Pages)

A self-contained, single-file ticker widget for `hd2clans.com/api/public/heroes26`.
Rotates between 4 panels: Total Raised, Top Fundraisers, Supporting Campaigns, Top Donors.
For the three list-style panels, entries display one at a time on a single line
(rank, avatar, name, amount) and cycle through automatically before moving on
to the next panel.

## Setup

1. Push `index.html` to a GitHub repo, enable **GitHub Pages** (Settings → Pages → branch `main`, root).
2. Your ticker URL will be `https://<username>.github.io/<repo>/`.
3. In OBS: **Sources → Add → Browser Source**, paste the URL (with any params you want, see below),
   set width/height to match your scene, check **"Shutdown source when not visible"** off,
   and you're done — background is transparent by default.

## URL Parameters

All parameters are optional and go after `?` on the URL, joined with `&`.

### Colors
Pass hex without `#` (since `#` needs URL-encoding) — `bar=00ff88` works, `#00ff88` does not need encoding if you do encode it as `%2300ff88`. Either works.

| Param | Default | Description |
|---|---|---|
| `bg` | `transparent` | Page background color |
| `bar` | `#2ecc71` | Progress bar fill / amount text color |
| `track` | `rgba(255,255,255,0.15)` | Progress bar empty-track color |
| `text` | `#ffffff` | Main text color |
| `accent` | `#ffcc00` | Panel titles, rank numbers, avatar border |
| `subtext` | `rgba(255,255,255,0.75)` | Secondary/smaller text |
| `font` | `'Segoe UI', Arial, sans-serif` | Font family |

### Size
| Param | Default | Description |
|---|---|---|
| `w` | (auto, fills browser source) | Force fixed pixel width |
| `h` | (auto, fills browser source) | Force fixed pixel height |

Usually easiest to just leave these unset and size the OBS Browser Source itself.

### Behavior
| Param | Default | Description |
|---|---|---|
| `rotate` | `8` | Seconds the **Total Raised** panel is shown before advancing (this panel has no sub-items to step through) |
| `entry` | `5` | Seconds each individual entry is shown within a list panel (fundraisers/campaigns/donors), before stepping to the next entry. After the last entry, it moves straight to the next panel — no looping back. |
| `refresh` | `30` | Seconds between re-fetching the API |
| `maxitems` | `5` | Max entries cycled through per list panel (fundraisers/campaigns/donors) |
| `panels` | `total,fundraisers,campaigns,donors` | Comma list — which panels to show & in what order. E.g. `panels=total,donors` shows only those two, alternating. |
| `api` | `https://hd2clans.com/api/public/heroes26` | Override the data source URL |

### Text overrides
| Param | Default |
|---|---|
| `title_total` | `Total Raised` |
| `title_fundraisers` | `Top Fundraisers` |
| `title_campaigns` | `Supporting Campaigns` |
| `title_donors` | `Top Donors` |
| `label_total` | `raised so far` (small text next to the dollar amount) |

## Examples

Default look, rotates every 8s:
```
https://you.github.io/charity-ticker/
```

Custom brand colors, 10s rotation, top 3 only, fixed 900x180 box for OBS:
```
https://you.github.io/charity-ticker/?bar=ff4444&accent=ffd700&rotate=10&maxitems=3&w=900&h=180
```

Only show the total + top donors, custom title:
```
https://you.github.io/charity-ticker/?panels=total,donors&title_total=Save%20the%20Children%20Goal
```

## Notes on CORS

This widget fetches the API directly from the browser. If `hd2clans.com` does not send
`Access-Control-Allow-Origin` headers permitting your GitHub Pages origin, the fetch will
fail silently in the browser console (you'll see a "Unable to load data" message on first
load). If that happens, you'll need a small proxy (e.g. a Cloudflare Worker or similar) to
re-serve the JSON with permissive CORS headers, and point `?api=` at that instead.

## Data field mapping

- **Total Raised**: `total_raised`, `pct` (progress bar %)
- **Top Fundraisers**: `fundraisers[].name`, `fundraisers[].amount.value`, `fundraisers[].avatar.src`
- **Supporting Campaigns**: `supporting_campaigns[].campaign_name` (falls back to `.v` if that key is used instead), `.amount_raised`, `.user_name`, `.user_avatar_url`
- **Top Donors**: `top_donors[].name`, `top_donors[].amount.value`
