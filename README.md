# CPL EDGE v1 dataset

A 0–100 composite measure of franchise strength in the Caribbean Premier
League, same methodological family as MLC / PSL / BPL / LPL EDGE — six
independently interpretable components, season-adjusted, frozen
calibration for out-of-sample scoring.

Built from the uploaded Cricsheet JSON archive.

![CPL EDGE trajectories](visuals/cpl_edge_trajectories.png)

## Calibration check: clean

Same reconstruction test as the other three leagues — rebuilt `EDGE` from
`EDGE_z` via `(rank − 0.5)/N_hist × 100` on pre-2026 rows and
`percentileofscore` for 2026 against that frozen distribution. Matched the
stored values exactly. No correction needed.

## Coverage

14 seasons (2013–2026), 85 team-season rows, 13 team names — but franchise
continuity here sits much closer to PSL than to BPL/LPL: **Guyana Amazon
Warriors played all 14 seasons**, and four more names (St Kitts and Nevis
Patriots, Jamaica Tallawahs, Trinbago Knight Riders, Barbados Tridents)
played 9+. Only 5 of the 13 names appear in 2 or fewer seasons — mostly
genuine rebrands (Trinidad & Tobago Red Steel → Trinbago Knight Riders;
Barbados Tridents → Barbados Royals; St Lucia Zouks → Stars → Kings;
Antigua Hawksbills → Antigua and Barbuda Falcons).

That's enough continuity for a **trajectory chart** rather than a heatmap
— but the first version of this chart had a real bug worth noting: naively
connecting each team's *existing* data points in sequence draws a straight
line across seasons a team didn't play (e.g. Barbados Tridents sat out
2020–2025, and a naive line would falsely connect their 2020 and 2026
scores as if it were one continuous trend). Fixed by reindexing each
team's series across the full season range with explicit gaps, so
matplotlib breaks the line rather than interpolating across years the
team didn't exist. Worth checking for the same issue if PSL's chart script
gets reused on a less-continuous league later — it happened to not
matter there only because PSL's teams didn't have mid-history gaps of more
than one season.

9 matches excluded entirely from EDGE calculations:

| Season | Match | Result |
|---|---|---|
| 2015 | St Lucia Zouks vs Trinidad & Tobago Red Steel | no result |
| 2016 | Jamaica Tallawahs vs Barbados Tridents | no result |
| 2017 | St Lucia Stars vs St Kitts and Nevis Patriots | no result |
| 2019 | Trinbago Knight Riders vs St Kitts and Nevis Patriots | tie |
| 2019 | St Lucia Zouks vs Trinbago Knight Riders | no result |
| 2020 | St Kitts and Nevis Patriots vs Jamaica Tallawahs | no result |
| 2021 | Trinbago Knight Riders vs Guyana Amazon Warriors | tie |
| 2023 | Trinbago Knight Riders vs St Kitts and Nevis Patriots | no result |
| 2023 | Guyana Amazon Warriors vs St Lucia Kings | no result |

Full list with match IDs: `excluded_matches.csv`.

## Method

Identical to the rest of the EDGE family (PSL / BPL / LPL) — same six
components, shrinkage constants, z-scoring and sign-inversion rules. See
PSL EDGE v1's README for the full formula writeup. Held-out calibration
season: `2026`.

## Files

- `team_match_edge.csv` — audit-level team-match inputs and derived rates.
- `team_season_edge_v1.csv` — final component scores and EDGE v1 (calibration verified, unchanged from upload).
- `excluded_matches.csv` — matches excluded because no winner/outcome was recorded.
- `visuals/cpl_edge_trajectories.png` — franchise EDGE by season.

## What this package does not do

Same non-goals as the rest of the EDGE family: no recency weighting, no
opponent adjustment or in-season rolling updates, no weights fit to
observed wins, no roster/player-availability effects, and no attempt to
formally link rebranded franchises (e.g. Red Steel → Trinbago Knight
Riders) into one continuous lineage — each name is scored as its own
entity even where a real-world succession is obvious from the record.
