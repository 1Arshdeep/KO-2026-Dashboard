# Claude Code Prompt — Update KO 2026 Dashboard: Saturday Evening Schedule + Live Standings

## Repository
- `index.html` — public live dashboard (Soccer / Basketball / Volleyball). Reads state from Firebase in real time.
- `admin.html` — PIN-locked scorekeeper; writes scores, DQ flags, delays to Firebase.
- `KO-2026-Brackers/index.html` — secondary copy; keep in sync with `index.html`.

## HARD SCOPE
Only change **Basketball** and **Volleyball**. Leave **Soccer** (double-elimination) exactly as it is. Do not break Firebase sync, live scoring, mobile layout, or the TV screens.

---

## PHASE 1 — Analyze first (no feature code yet)
Read `index.html`, `admin.html`, and `KO-2026-Brackers/index.html`. Summarize: the game data schema and how games are currently generated (`BB_SLOTS`, `VB_SLOTS`, `buildAllGames`, any `*_SCHEMA_V` version constants); Firebase read/write flow; how basketball & volleyball currently render + auto-advance as brackets; how DQ works today; and how the TV screens work (rotating carousel + hash routes like `#tv/gym-1`, the TV picker modal). Produce an architecture summary + migration plan and **pause for review**.

---

## PHASE 2 — Replace Basketball & Volleyball with this FIXED schedule
Remove the elimination-bracket generation for these two sports and drive them from the exact fixed game lists below (this is the finalized Saturday evening schedule — every basketball team plays exactly 3 games, every volleyball team exactly 2, no back-to-backs, no dual-sport overlaps).

- Basketball: **2 courts** (Court A, Court B), 20-min games, 5:00–11:00 PM.
- Volleyball: **1 court** (Gym 1), 15-min games, 5:00–11:00 PM.
- Bump the schema-version constant(s) so cached client state rebuilds under the new games.

```js
const BASKETBALL_GAMES = [
  { start: "5:00 PM", end: "5:20 PM", court: "Court A", team1: "NPSS", team2: "Farmers Elite" },
  { start: "5:00 PM", end: "5:20 PM", court: "Court B", team1: "Brockish", team2: "Icemen" },
  { start: "5:30 PM", end: "5:50 PM", court: "Court A", team1: "KLF", team2: "KGA" },
  { start: "5:30 PM", end: "5:50 PM", court: "Court B", team1: "McMaster University", team2: "Kaurs United" },
  { start: "6:00 PM", end: "6:20 PM", court: "Court A", team1: "Sharomani Akaali Dal", team2: "Farmers Elite" },
  { start: "6:00 PM", end: "6:20 PM", court: "Court B", team1: "NPSS", team2: "Icemen" },
  { start: "6:30 PM", end: "6:50 PM", court: "Court A", team1: "Babbar Khalsa Ballers", team2: "Dixie United" },
  { start: "6:30 PM", end: "6:50 PM", court: "Court B", team1: "Sher Elite", team2: "KLF" },
  { start: "7:00 PM", end: "7:20 PM", court: "Court A", team1: "Sharomani Akaali Dal", team2: "Brockish" },
  { start: "7:00 PM", end: "7:20 PM", court: "Court B", team1: "McMaster University", team2: "Panjab Lions" },
  { start: "7:30 PM", end: "7:50 PM", court: "Court A", team1: "Sher Elite", team2: "Akaal Fauj" },
  { start: "7:30 PM", end: "7:50 PM", court: "Court B", team1: "Babbar Khalsa Ballers", team2: "Kaurs United" },
  { start: "8:00 PM", end: "8:20 PM", court: "Court A", team1: "Dixie United", team2: "Sharomani Akaali Dal" },
  { start: "8:30 PM", end: "8:50 PM", court: "Court A", team1: "KGA", team2: "Panjab Lions" },
  { start: "9:00 PM", end: "9:20 PM", court: "Court A", team1: "KLF", team2: "Kaurs United" },
  { start: "9:00 PM", end: "9:20 PM", court: "Court B", team1: "Akaal Fauj", team2: "Farmers Elite" },
  { start: "9:30 PM", end: "9:50 PM", court: "Court A", team1: "Sher Elite", team2: "Babbar Khalsa Ballers" },
  { start: "9:30 PM", end: "9:50 PM", court: "Court B", team1: "Icemen", team2: "Panjab Lions" },
  { start: "10:00 PM", end: "10:20 PM", court: "Court A", team1: "Brockish", team2: "KGA" },
  { start: "10:00 PM", end: "10:20 PM", court: "Court B", team1: "Dixie United", team2: "NPSS" },
  { start: "10:30 PM", end: "10:50 PM", court: "Court A", team1: "Akaal Fauj", team2: "McMaster University" },
];

const VOLLEYBALL_GAMES = [
  { start: "5:20 PM", end: "5:35 PM", court: "Gym 1", team1: "Chinguacousy Ekta", team2: "Babbar Khalsa Ballers" },
  { start: "5:40 PM", end: "5:55 PM", court: "Gym 1", team1: "Dixie United", team2: "Brockish" },
  { start: "6:00 PM", end: "6:15 PM", court: "Gym 1", team1: "Panjab Lions", team2: "Brampton Spikers" },
  { start: "6:20 PM", end: "6:35 PM", court: "Gym 1", team1: "McMaster University", team2: "Chinguacousy Ekta" },
  { start: "6:40 PM", end: "6:55 PM", court: "Gym 1", team1: "Kaurs United", team2: "Baba Bidhi Chand Chinna VC" },
  { start: "7:40 PM", end: "7:55 PM", court: "Gym 1", team1: "Palm Springs", team2: "Panjab Lions" },
  { start: "8:00 PM", end: "8:15 PM", court: "Gym 1", team1: "Brockish", team2: "McMaster University" },
  { start: "8:20 PM", end: "8:35 PM", court: "Gym 1", team1: "Babbar Khalsa Ballers", team2: "Vortex" },
  { start: "8:40 PM", end: "8:55 PM", court: "Gym 1", team1: "Sharomani Akaali Dal", team2: "Dixie United" },
  { start: "9:20 PM", end: "9:35 PM", court: "Gym 1", team1: "Baba Bidhi Chand Chinna VC", team2: "Sharomani Akaali Dal" },
  { start: "9:40 PM", end: "9:55 PM", court: "Gym 1", team1: "KLF", team2: "Palm Springs" },
  { start: "10:00 PM", end: "10:15 PM", court: "Gym 1", team1: "Brampton Spikers", team2: "Kaurs United" },
  { start: "10:20 PM", end: "10:35 PM", court: "Gym 1", team1: "Vortex", team2: "KLF" },
];
```

Each game object should map onto the existing game schema (id, sport, court, team1, team2, score1, score2, status, dq, etc.). Keep start/end times for display and the live "now" logic. There is **no bracket progression** for BB/VB anymore — these are all the games.

---

## PHASE 3 — Standings (replace the bracket views)
Replace the Basketball and Volleyball bracket views on their tabs with a **live standings table**: Rank · Team · Games Played · Wins · Losses · Score Differential. Recompute live whenever Firebase state updates.

Sort order:
- **Basketball:** by **wins** (desc), tiebreak by **cumulative score differential** (sum of points-for − points-against), higher first.
- **Volleyball:** by **wins** (desc), tiebreak by **score differential**, higher first.

> ⚠️ CONFIRM before building: for volleyball, rank by **wins then score differential** (my assumption) or by **total points scored**? Ask if unsure.

---

## PHASE 4 — Admin DQ behavior
In `admin.html`, when a **basketball** team is marked **Disqualified** (no-show): credit the opponent a **win with a +10 score differential** (e.g. record 10–0, or +10/−10 — pick one representation and keep standings math consistent). The DQ'd team takes the loss.

> ⚠️ CONFIRM: exact DQ scoreline (10–0 vs differential-only), and whether **volleyball** DQs use the same +10 rule (only basketball was specified).

---

## PHASE 5 — Live TV standings screens
Add two hash-addressable TV screens matching the existing `#tv/...` routing and broadcast styling:
- `#tv/standings-basketball`
- `#tv/standings-volleyball`

Each renders the live standings table (Rank · Team · W–L · Score Differential), auto-updating from Firebase. Add both to the TV picker modal and the rotating carousel.

---

## Constraints & acceptance
- Preserve Firebase sync, live scoring, soccer behavior, mobile layout, all TV modes; keep single-file architecture; keep `KO-2026-Brackers/index.html` in sync.
- Work in stages, test each (enter sample scores → verify standings + tiebreak; verify DQ +10; verify both TV screens update live). Show diffs.
- Done when: BB & VB tabs show live standings (not brackets) from the fixed schedule above; standings sort by wins then differential; a basketball DQ gives +10 to the opponent; two live TV standings screens exist and update live; soccer unchanged.

Ask the two CONFIRM questions before Phase 3.
