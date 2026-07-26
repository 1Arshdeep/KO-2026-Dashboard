# Claude Code Prompt — Finalize BASKETBALL standings & lock the Day-2 bracket (basketball only)

## Context (read first)
Some Saturday basketball games were **deleted from Firebase**, so the live-computed basketball standings are unreliable. Lock the **FINAL basketball standings** and the resulting **top-8 knockout seeds/matchups** to match the official end-of-Day-1 standings below. Do **not** let live re-computation override these for the knockout.

**Scope rule:** change **BASKETBALL only**. Do not touch soccer or volleyball standings/brackets, Firebase sync, live scoring, mobile layout, or TV modes.

## Files (keep all three identical)
`index.html` (public), `admin.html` (scorekeeper), `KO-2026-Brackers/index.html` (secondary copy).

## PHASE 1 — Analyze, then pause
Find: how basketball standings are computed/rendered, how the basketball bracket resolves seeds (`#1…#8`, `BBQF1W`, `BBSF1L`, etc.), the schema-version constant, and Firebase sync. Report a short plan and **pause for review**.

## PHASE 2 — Lock the FINAL basketball standings
Add a hardcoded `BB_FINAL_STANDINGS` array (order = final rank). When present, the dashboard uses it to render the basketball standings table **and** resolve the knockout seeds — instead of recomputing from live scores. Bump the schema-version constant.

**Includes a game missing from the DB:** *Akaal Fauj defeated Sharomani Akaali Dal* — Akaal Fauj is credited the win, Sharomani Akaali Dal the loss (games-played unchanged: Akaal Fauj W2→W3 L2→L1; Sharomani Akaali Dal W2→W1 L0→L1).

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

Ranking rule: **wins → score differential**. Top 8 advance.

## PHASE 3 — Lock the Day-2 BASKETBALL bracket (top-8, Gym 2 & Gym 3)
- QF1 (3:30 PM, Gym 2): **KGA (#1) vs Brockish (#8)**
- QF2 (3:30 PM, Gym 3): **Sher Elite (#4) vs Dixie United (#5)**
- QF3 (3:55 PM, Gym 2): **KLF (#2) vs Kaurs United FC (#7)**
- QF4 (3:55 PM, Gym 3): **Akaal Fauj (#3) vs Babbar Khalsa Ballers (#6)**
- SF1 (4:20 PM, Gym 2): QF1 Winner vs QF2 Winner
- SF2 (4:20 PM, Gym 3): QF3 Winner vs QF4 Winner
- 3rd place (4:45 PM, Gym 2): SF1 Loser vs SF2 Loser
- Final (5:40 PM, Gym 2, 30 min): SF1 Winner vs SF2 Winner

## Verify before done
1. Basketball standings render exactly as the table above — **Akaal Fauj is 3-1 at #3**, **Sharomani Akaali Dal is 1-1 at #9** (out).
2. The basketball bracket shows the four quarterfinals exactly as in Phase 3.
3. Deleting/editing a Day-1 basketball score no longer changes the locked standings or bracket.
4. Soccer and volleyball are completely unchanged.
5. `index.html`, `admin.html`, `KO-2026-Brackers/index.html` are identical; schema bumped; Day-2 live scoring still works.

Show diffs per file and run checks 1–4 before declaring done.
