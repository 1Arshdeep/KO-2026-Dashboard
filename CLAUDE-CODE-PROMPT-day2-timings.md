# Claude Code Prompt — Update the Day-2 knockout TIMINGS (all sports)

## Context
The Sunday (Day-2) knockout start times and game lengths are now confirmed. Update the schedule times/durations for all three sports on the public dashboard and admin, keeping the already-locked seeds/matchups and standings unchanged.

## Files (keep all three identical)
`index.html` (public), `admin.html` (scorekeeper), `KO-2026-Brackers/index.html`.

## PHASE 1 — Analyze, then pause
Find where the Day-2 knockout games' times and per-game durations (`slotMin` or equivalent) are defined for soccer, basketball, volleyball. Report a short plan and **pause**.

## PHASE 2 — Set these exact Day-2 times & durations
Bump the schema-version constant after editing.

### ⚽ Soccer — Fields A & B (1:15–2:45 PM)
| Round | Time | Field | Length |
|---|---|---|---|
| Semifinal 1 | 1:15 PM | Field A | 20 min |
| Semifinal 2 | 1:15 PM | Field B | 20 min |
| 3rd Place | 1:45 PM | Field A | 20 min |
| Final | 2:15 PM | Field A | 30 min (2-min halftime inside) |
*(10-min breaks between rounds; pack up & move to gyms at 2:50 PM.)*

### 🏀 Basketball — Gym 2 & Gym 3 (3:15–6:15 PM)
| Round | Time | Court | Length |
|---|---|---|---|
| Quarterfinal 1 | 3:15 PM | Gym 2 | 20 min |
| Quarterfinal 2 | 3:15 PM | Gym 3 | 20 min |
| Quarterfinal 3 | 3:45 PM | Gym 2 | 20 min |
| Quarterfinal 4 | 3:45 PM | Gym 3 | 20 min |
| Semifinal 1 | 4:15 PM | Gym 2 | 20 min |
| Semifinal 2 | 4:15 PM | Gym 3 | 20 min |
| 3rd Place | 4:45 PM | Gym 2 | 20 min |
| Final | 5:40 PM | Gym 2 | 30 min (2-min halftime inside) |
*(10-min breaks between rounds; break after bronze to watch the volleyball final, then the basketball final at 5:40.)*

### 🏐 Volleyball — Gym 1 (3:15–5:40 PM)
| Round | Time | Court | Length |
|---|---|---|---|
| Semifinal 1 | 3:15 PM | Gym 1 | 25 min — best of 3 sets, cap 15 |
| Semifinal 2 | 3:45 PM | Gym 1 | 25 min — best of 3 sets, cap 15 |
| 3rd Place | 4:20 PM | Gym 1 | 25 min — best of 3 sets, cap 15 |
| Final | 4:55 PM | Gym 1 | 45 min — best of 3 sets, cap 21 |
*(5-min break after SF1; 10-min breaks after SF2 and bronze.)*

## Notes
- **Keep the locked seeds, matchups, and Day-1 final standings exactly as they are** — this change is times/durations only.
- Volleyball and basketball run in **parallel** in separate gyms; no team is in both playoffs, so there are no clashes. Volleyball final (4:55–5:40) and basketball final (5:40–6:15) do not overlap.

## Verify before done
1. Each sport's Day-2 rounds show the exact times and lengths in the tables above (basketball final 5:40–6:15; volleyball final 4:55–5:40; soccer final 2:15–2:45).
2. Seeds/matchups and standings are unchanged; admin advancement still works.
3. `index.html`, `admin.html`, `KO-2026-Brackers/index.html` identical; schema bumped; Firebase sync intact.

Show diffs and run checks 1–2 before declaring done.
