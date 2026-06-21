# Charity Ticker (OBS / GitHub Pages)

A self-contained, single-file ticker widget for `hd2clans.com/api/public/heroes26`.
Rotates between 4 panels: Total Raised, Top Fundraisers, Supporting Campaigns, Top Donors.
For the three list-style panels, entries display one at a time on a single line
(rank, avatar, name, amount) and cycle through automatically before moving on
to the next panel.

## Setup


1. In OBS: **Sources → Add → Browser Source**, paste the URL (with any params you want, see below),
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
| `font` | `'FSSinclair', 'Segoe UI', Arial, sans-serif` | Font family. The default loads a custom font (FS Sinclair) directly from a GitHub-hosted `.otf` file via `@font-face`; override this if you want a different font instead. |

### Position
| Param | Default | Description |
|---|---|---|
| `position` | `bottom` | Where the ticker bar sits vertically on the page: `top`, `center`, or `bottom`. Any other value falls back to `bottom`. |

### Size
| Param | Default | Description |
|---|---|---|
| `w` | (auto, fills browser source) | Force fixed pixel width |
| `h` | (auto, fills browser source) | Force fixed pixel height |

Usually easiest to just leave these unset and size the OBS Browser Source itself.
With `position`, the bar will hug the top/center/bottom edge of whatever box
(browser source or window) it's given — handy for OBS if you want the ticker
to sit flush along one edge of the canvas instead of vertically centered.

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

Default look, rotates every 8s, bar pinned to the bottom:
```
https://adeafblindman.github.io/Heros2026/
```

Bar pinned to the top instead:
```
https://adeafblindman.github.io/Heros2026/?position=top
```

Custom brand colors, 10s rotation, top 3 only, fixed 900x180 box for OBS:
```
https://adeafblindman.github.io/Heros2026/?bar=ff4444&accent=ffd700&rotate=10&maxitems=3&w=900&h=180
```

Only show the total + top donors, custom title, centered vertically:
```
https://adeafblindman.github.io/Heros2026/?panels=total,donors&title_total=Save%20the%20Children%20Goal&position=center
```

## Custom font

By default the ticker loads **FS Sinclair** from jsDelivr's GitHub CDN mirror:
```
https://cdn.jsdelivr.net/gh/adeafblindman/Heros2026@main/FSSinclair-Medium.otf
```
via `@font-face` in the page's `<style>` block. This uses jsDelivr rather than GitHub's own
raw file host (`github.com/.../raw/...`) because GitHub serves an empty
`Access-Control-Allow-Origin` header on raw files, which browsers treat as a hard CORS
block — the font would silently fail to load. jsDelivr mirrors public GitHub repos and adds
proper CORS headers, so it works.

You can always override the font entirely with `?font=` if needed.

## Data field mapping

- **Total Raised**: `total_raised`, `pct` (progress bar %)
- **Top Fundraisers**: `fundraisers[].name`, `fundraisers[].amount.value`, `fundraisers[].avatar.src`
- **Supporting Campaigns**: `supporting_campaigns[].campaign_name` (falls back to `.v` if that key is used instead), `.amount_raised`, `.user_name`, `.user_avatar_url`
- **Top Donors**: `top_donors[].name`, `top_donors[].amount.value`
