# Charity Ticker (OBS / GitHub Pages)

A self-contained, single-file ticker widget for `hd2clans.com/api/public/heroes26`.
Rotates between 5 panels: Total Raised, Top Fundraisers, Supporting Campaigns, Top Donors, and Top Teams.
For the four list-style panels, entries display one at a time on a single line
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
| `entry` | `5` | Seconds each individual entry is shown within a list panel (fundraisers/campaigns/donors/teams), before stepping to the next entry. After the last entry, it moves straight to the next panel — no looping back. |
| `refresh` | `30` | Seconds between re-fetching the API |
| `maxitems` | `5` | Max entries cycled through per list panel (fundraisers/campaigns/donors/teams) |
| `panels` | `total,fundraisers,campaigns,donors` | Comma list — which panels to show & in what order. E.g. `panels=total,donors` shows only those two, alternating. **Top Teams is not shown by default** — add `teams` to this list if you want it (e.g. `panels=total,fundraisers,campaigns,donors,teams`). |
| `api` | `https://hd2clans.com/api/public/heroes26` | Override the data source URL |
| `fundraisers` | (none — shows all campaigns) | Filter **Supporting Campaigns** down to a single campaign, matched against that campaign's `campaign_url` (recommended) or, as a fallback, an *exact* match on `campaign_slug`. **Use the full campaign URL where possible** — `campaign_slug` is not guaranteed unique (multiple campaigns can share the same auto-generated default slug), so slug-only matching can be ambiguous. *(Renamed from `team` — see below.)* |
| `team` | (none — shows all teams) | Filter **Top Teams** down to a single team, matched against that team's `url` from `team_leaderboard`. Accepts either the full URL or just the relative path (e.g. `/+2nd-liberation-battalion/profile`). |

> ⚠️ **Heads up on `+` characters:** some team/campaign URLs and slugs contain a literal `+`
> (e.g. `/+2nd-liberation-battalion/profile`). In a query string, an unencoded `+` is read by
> the browser as a space, not a plus sign — which will break the match. Always percent-encode
> it as `%2B` when building the `team=` or `fundraisers=` value. See the example below.

### Text overrides
| Param | Default |
|---|---|
| `title_total` | `Total Raised` |
| `title_fundraisers` | `Top Fundraisers` |
| `title_campaigns` | `Supporting Campaigns` |
| `title_donors` | `Top Donors` |
| `title_teams` | `Top Teams` |
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

Show only one specific campaign in the Supporting Campaigns panel (e.g. for a streamer who
only wants their own team's campaign on screen, not the whole event):
```
https://adeafblindman.github.io/Heros2026/?fundraisers=https://tiltify.com/@tana-sukke/helldivers-2-heroes-26-fundraiser-for-save-the-children
```
Since only one campaign will match, that panel will just show it on a loop without cycling
through others.

Show the Top Teams panel, pinned to one specific team (note the `%2B` in place of the `+`
in the team's slug, and that `teams` has to be added to `panels` since it's off by default):
```
https://adeafblindman.github.io/Heros2026/?panels=teams&team=/%2B2nd-liberation-battalion/profile
```
The full `https://tiltify.com/...` form also works the same way, as long as the `+` is still
encoded as `%2B`:
```
https://adeafblindman.github.io/Heros2026/?panels=teams&team=https://tiltify.com/%2B2nd-liberation-battalion/profile
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
- **Top Teams**: `team_leaderboard[].name`, `team_leaderboard[].amount.value`, `team_leaderboard[].avatar.src`, `team_leaderboard[].url` (used for the `?team=` filter)
