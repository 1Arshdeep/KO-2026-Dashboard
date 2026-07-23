# Task: Convert Basketball & Volleyball from elimination brackets to a live STANDINGS system

## Repository context
This repo powers the Khalsa Olympics 2026 live tournament dashboards:
- `index.html` — public live dashboard (Soccer / Basketball / Volleyball tabs, bracket views, schedule, TV screens). Reads state from Firebase in real time.
- `admin.html` — PIN-locked scorekeeper app. Writes scores, DQ flags, and delays to Firebase.
- `KO-2026-Brackers/index.html` — a secondary copy of the dashboard; keep it consistent with `index.html`.

State currently syncs admin → public via Firebase Realtime DB. All three sports currently use elimination brackets (soccer = double elim, basketball = double elim, volleyball = single elim) with automatic winner/loser advancement.

## Hard scope rule
**Only change Basketball and Volleyball. Do NOT change Soccer** — soccer stays double-elimination exactly as it is today. Do not break the existing Firebase sync, live scoring, mobile layout, or TV modes.

---

## PHASE 1 — Deep analysis FIRST (do not write any feature code yet)
Read `index.html`, `admin.html`, and `KO-2026-Brackers/index.html` in full and produce a written summary of:
1. The **game data schema** (fields on each game object) and how games are generated (look for `BB_SLOTS`, `VB_SLOTS`, `buildAllGames`, schema-version constants like `BB_SCHEMA_V`).
2. **State management + Firebase sync** — how the admin writes and how the public view reads/renders on update.
3. How **brackets render and auto-advance** today for basketball and volleyball specifically.
4. How **DQ (disqualification)** currently works in admin and what it writes to state.
5. How the **TV screens** work — the rotating carousel AND the hash-addressable routes (e.g. `#tv/gym-1`, `#tv/gym-2`), the TV picker modal, gallery/finals screens.
6. Any **hardcoded assumptions** (team counts, round names, seed logic).

Output this as an architecture summary + a concrete migration plan. **Pause and let me review the plan before coding.**

---

## PHASE 2 — New design (Basketball & Volleyball → standings)
Replace the elimination brackets for these two sports with a **round-robin-style standings model**. Each team plays a fixed set of games (no winners/losers bracket, no auto-advancement):
- **Basketball:** 14 teams, each plays exactly **2 games**.
- **Volleyball:** 13 teams, each plays exactly **2 games** (matches come from the fixed schedule, not from bracket progression).

Replace the bracket view on each sport's tab with a **live standings table**: Rank · Team · Games Played · Wins · Losses · Score Differential.

**Standings sort order:**
- **Basketball:** sort by **wins** (descending); break ties by **cumulative score differential** (sum of points-for minus points-against across all games) — higher differential ranks higher.
- **Volleyball:** sort by **wins** (descending); break ties by **score differential** — higher ranks higher.

> ⚠️ CONFIRM WITH ME before building: for volleyball I described it as "whoever has the most score." Clarify which you want — (a) rank by **wins then score differential** (same as basketball, my assumption), or (b) rank by **total points scored**. Ask me if unsure; do not guess.

Score differential must recompute live from entered scores every time state updates.

---

## PHASE 3 — Admin DQ behavior
In `admin.html`, when a team is marked **Disqualified** (no-show) for a **basketball** match:
- The opponent is credited a **win with a +10 score differential** (e.g. record the game as 10–0, or credit +10 to the opponent and −10 to the DQ'd team — pick one representation and apply it consistently so standings math stays correct).
- The DQ'd team takes the loss.

> ⚠️ CONFIRM: (1) exact scoreline representation for a DQ (10–0 vs. differential-only), and (2) whether **volleyball** DQs use the same +10 rule — I only specified basketball. Ask before implementing volleyball DQ.

---

## PHASE 4 — Live TV standings screens
Add two new **hash-addressable** TV screens, consistent with the existing `#tv/...` routing and broadcast styling:
- `#tv/standings-basketball`
- `#tv/standings-volleyball`

Each renders the live standings table (Rank · Team · W–L · Score Differential), auto-updating from the same Firebase sync — no manual refresh. Match the existing TV aesthetic (fonts, colors, big readable rows). Add both to the **TV picker modal** and include them in the **rotating carousel**.

---

## Constraints & quality bar
- Preserve Firebase realtime sync, live score entry, soccer's existing behavior, mobile responsiveness, and all current TV modes.
- Bump the relevant schema-version constant(s) so any cached client state rebuilds cleanly under the new model.
- Keep the single-file architecture (no new build tooling or frameworks).
- Keep `KO-2026-Brackers/index.html` in sync with `index.html`.
- Work in small stages and test each: enter sample scores and verify standings math, verify tiebreak by differential, verify DQ credits +10 correctly, verify both new TV screens update live. Show diffs at each stage.

## Acceptance criteria
- Basketball and Volleyball tabs show live standings tables (not brackets) that update as scores are entered.
- Standings sort by wins, then score differential, for both sports.
- A basketball DQ in admin gives the opponent a +10 differential and a win.
- Two new live TV standings screens exist, are reachable by URL hash and from the TV picker, and update live.
- Soccer is completely unchanged.

Before Phase 2, ask me the CONFIRM questions above.
