# Claude Code Prompt — Finalize Day 1 standings & lock the Day 2 knockout brackets

## Context (read first)
Some Saturday (Day 1) games were **deleted from Firebase**, so the dashboard's live-computed standings are now unreliable. We need to **lock the FINAL Day-1 standings** and the resulting **knockout seeds/matchups** to match the official standings below (taken from the dashboard screenshots at the end of Day 1). Do **not** let live re-computation override these for the Day-2 knockout.

## Files (keep all three identical)
`index.html` (public), `admin.html` (scorekeeper), `KO-2026-Brackers/index.html` (secondary copy).

## PHASE 1 — Analyze, then pause
Read all three files and find: how standings are computed/rendered per sport, how the knockout bracket resolves seeds (tokens like `#1`, `BBQF1W`, `A1`, `VBSF1`), the DQ handling, the schema-version constant, and Firebase sync. Report a short plan and **pause for review**.

## PHASE 2 — Add a FINAL-standings override (so deleted DB games don't matter)
For each sport, add a hardcoded `FINAL_STANDINGS` array (order = final rank). When present, the dashboard uses it to (a) render the standings table and (b) resolve knockout seeds — instead of recomputing from live scores. Bump the schema-version constant so caches rebuild.

### 🏀 BASKETBALL — final standings (top 8 advance)
Includes a game that was missing from the DB: **Akaal Fauj defeated Sharomani Akaali Dal** (Akaal Fauj credited the win, Sharomani Akaali Dal the loss — games-played unchanged).

| # | Team | GP | W | L | Diff | |
|---|------|----|---|---|------|--|
| 1 | KGA | 3 | 3 | 0 | +61 | ADV |
| 2 | KLF | 3 | 3 | 0 | +15 | ADV |
| 3 | Akaal Fauj | 4 | 3 | 1 | −45 | ADV |
| 4 | Sher Elite | 3 | 2 | 1 | +36 | ADV |
| 5 | Dixie United | 2 | 2 | 0 | +18 | ADV |
| 6 | Babbar Khalsa Ballers | 3 | 2 | 1 | +10 | ADV |
| 7 | Kaurs United FC | 3 | 2 | 1 | +7 | ADV |
| 8 | Brockish | 3 | 2 | 1 | −6 | ADV |
| 9 | Sharomani Akaali Dal | 2 | 1 | 1 | +20 | |
| 10 | Icemen | 3 | 0 | 3 | −12 | |
| 11 | McMaster University | 2 | 0 | 2 | −20 | |
| 12 | Panjab Lions | 3 | 0 | 3 | −25 | |
| 13 | NPSS | 3 | 0 | 3 | −29 | |
| 14 | Farmers Elite | 3 | 0 | 3 | −30 | |

### 🏐 VOLLEYBALL — final standings (top 4 advance)

| # | Team | GP | W | L | Diff | |
|---|------|----|---|---|------|--|
| 1 | Waymakers | 2 | 2 | 0 | +32 | ADV |
| 2 | Palm Springs | 2 | 2 | 0 | +25 | ADV |
| 3 | Baba Bidhi Chand Chinna VC | 2 | 2 | 0 | +22 | ADV |
| 4 | Chinguacousy Ekta | 2 | 2 | 0 | +14 | ADV |
| 5 | Brampton Spikers | 2 | 1 | 1 | +20 | |
| 6 | Dixie United | 2 | 1 | 1 | +9 | |
| 7 | KLF | 2 | 1 | 1 | −1 | |
| 8 | Panjab Lions | 2 | 1 | 1 | −1 | |
| 9 | Babbar Khalsa Ballers | 2 | 1 | 1 | −3 | |
| 10 | Brockish | 2 | 1 | 1 | −12 | |
| 11 | McMaster University | 2 | 0 | 2 | −20 | |
| 12 | Vortex | 2 | 0 | 2 | −20 | |
| 13 | Sharomani Akaali Dal | 2 | 0 | 2 | −31 | |
| 14 | Kaurs United FC | 2 | 0 | 2 | −34 | |

### ⚽ SOCCER — final group standings (top 2 of each group advance)
**Group A:** 1) KLF (15 pts, +10) ADV · 2) Humberwood (14, +17) ADV · 3) Brockish (13) · 4) NPSS (10) · 5) Babbar Khalsa Ballers (6) · 6) Sharomani Akaali Dal (3) · 7) Panjab Lions (−15).
**Group B — advancing 2:** 1) Rebel FC ADV · 2) Mohali FC ADV. (Keep the rest of Group B's rows as currently stored; if they were affected by deleted games, ask me for the Group B numbers.)

## PHASE 3 — Lock the Day-2 knockout matchups
Set these directly (times/courts already in the schedule):

**⚽ Soccer (Fields A/B):**
- SF1 (1:15 PM, Field A): **KLF vs Mohali FC**
- SF2 (1:15 PM, Field B): **Rebel FC vs Humberwood**
- 3rd place (1:50, Field A): SF1 loser vs SF2 loser · Final (2:29, Field A): SF1 winner vs SF2 winner

**🏐 Volleyball (Gym 1):**
- SF1 (3:30 PM): **Waymakers (#1) vs Chinguacousy Ekta (#4)**
- SF2 (3:50 PM): **Palm Springs (#2) vs Baba Bidhi Chand Chinna VC (#3)**
- 3rd place (4:15): SF losers · Final (5:10): SF winners (best of 3 sets × 8 min)

**🏀 Basketball, TOP-8 (Gym 2 & 3):**
- QF1 (3:30, Gym 2): **KGA (#1) vs Brockish (#8)**
- QF2 (3:30, Gym 3): **Sher Elite (#4) vs Dixie United (#5)**
- QF3 (3:55, Gym 2): **KLF (#2) vs Kaurs United FC (#7)**
- QF4 (3:55, Gym 3): **Akaal Fauj (#3) vs Babbar Khalsa Ballers (#6)**
- SF1 (4:20, Gym 2): QF1 W vs QF2 W · SF2 (4:20, Gym 3): QF3 W vs QF4 W
- 3rd place (4:45, Gym 2): SF losers · Final (5:40, Gym 2, 30 min): SF winners

## Verify before done
1. Standings tables render exactly as the tables above (basketball shows Akaal Fauj 3-1 at #3, Sharomani Akaali Dal 1-1 at #9).
2. The three knockout brackets show the exact matchups in Phase 3.
3. Deleting/editing a Day-1 score no longer changes the locked standings or the brackets.
4. `index.html`, `admin.html`, `KO-2026-Brackers/index.html` are identical; schema bumped; Firebase sync and live scoring for Day-2 games still work.

Show diffs per file and run checks 1–3 before declaring done.
