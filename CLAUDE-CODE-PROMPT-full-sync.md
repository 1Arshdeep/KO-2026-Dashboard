# Claude Code Prompt — Sync the KO 2026 dashboard to the FINAL schedule (ALL sports)

This replaces the schedules and formats for **all three sports** with the finalized versions. The current dashboard is out of date (soccer 25-min, basketball 15-min/6-games, old volleyball) — this brings everything in line with the official "KO2026 Final Schedule".

## Files (keep all three identical)
- `index.html` (public), `admin.html` (scorekeeper), `KO-2026-Brackers/index.html` (secondary copy).

## PHASE 1 — Read first, then plan
Read all three files. Map: the slot schema + `buildAllGames`, the `SOC_SLOT`/`BB_SLOT`/`VB_SLOT` constants, `DEFAULT_SOCCER_GROUPS`, `defaultTeams()`, `resolveGroupToken`, how standings/brackets render, DQ handling, Firebase sync, and the TV/standings hash screens. Produce a short migration summary and **pause for review**.

## PHASE 2 — Game lengths + replace the three schedules
Set the slot (game) lengths:
```js
const SOC_SLOT = 20;   // soccer 20-min games (8-min break between 28-min slots; groups finish 4:28 PM)
const BB_SLOT  = 20;   // basketball 20-min games, 10-min break (clean 30-min rounds 5:00-10:00, ends 10:20 PM; Sunday final 30 via slotMin)
const VB_SLOT  = 15;   // volleyball 15-min Saturday games (Sunday games overridden via slotMin)
```
Bump any schema-version constant so cached client state rebuilds.

Keep the team lists as-is: `DEFAULT_SOCCER_GROUPS` (Group A: Babbar Khalsa Ballers, Sharomani Akaali Dal, KLF, NPSS, Humberwood, Brockish, Panjab Lions · Group B: Dixie United, Mohali FC, Akaal Fauj, Punjab Kings, McMaster University, Rebel FC, Kaurs United FC), basketball 14 teams, volleyball 14 teams.

Replace **`SOCCER_SLOTS`** entirely with:
```js
const SOCCER_SLOTS = [
  {day:1, time:"11:00", field:"Field A", group:"B", t1:"B3", t2:"B5"},
  {day:1, time:"11:00", field:"Field B", group:"A", t1:"A6", t2:"A4"},
  {day:1, time:"11:28", field:"Field A", group:"A", t1:"A2", t2:"A5"},
  {day:1, time:"11:28", field:"Field B", group:"B", t1:"B1", t2:"B4"},
  {day:1, time:"11:28", field:"Field C", group:"B", t1:"B2", t2:"B3"},
  {day:1, time:"11:28", field:"Field D", group:"A", t1:"A1", t2:"A4"},
  {day:1, time:"11:56", field:"Field A", group:"B", t1:"B1", t2:"B2"},
  {day:1, time:"11:56", field:"Field B", group:"B", t1:"B5", t2:"B7"},
  {day:1, time:"11:56", field:"Field C", group:"A", t1:"A5", t2:"A3"},
  {day:1, time:"11:56", field:"Field D", group:"A", t1:"A6", t2:"A2"},
  {day:1, time:"12:24", field:"Field A", group:"A", t1:"A1", t2:"A6"},
  {day:1, time:"12:24", field:"Field B", group:"B", t1:"B1", t2:"B7"},
  {day:1, time:"12:24", field:"Field C", group:"A", t1:"A7", t2:"A3"},
  {day:1, time:"12:24", field:"Field D", group:"B", t1:"B4", t2:"B5"},
  {day:1, time:"12:52", field:"Field A", group:"A", t1:"A2", t2:"A7"},
  {day:1, time:"12:52", field:"Field B", group:"A", t1:"A4", t2:"A5"},
  {day:1, time:"12:52", field:"Field C", group:"A", t1:"A1", t2:"A3"},
  {day:1, time:"12:52", field:"Field D", group:"B", t1:"B5", t2:"B6"},
  {day:1, time:"13:20", field:"Field A", group:"B", t1:"B1", t2:"B3"},
  {day:1, time:"13:20", field:"Field B", group:"B", t1:"B2", t2:"B7"},
  {day:1, time:"13:20", field:"Field C", group:"A", t1:"A5", t2:"A6"},
  {day:1, time:"13:20", field:"Field D", group:"B", t1:"B4", t2:"B6"},
  {day:1, time:"13:48", field:"Field A", group:"B", t1:"B2", t2:"B4"},
  {day:1, time:"13:48", field:"Field B", group:"A", t1:"A4", t2:"A2"},
  {day:1, time:"14:16", field:"Field A", group:"A", t1:"A2", t2:"A3"},
  {day:1, time:"14:16", field:"Field B", group:"A", t1:"A6", t2:"A7"},
  {day:1, time:"14:16", field:"Field C", group:"B", t1:"B2", t2:"B5"},
  {day:1, time:"14:16", field:"Field D", group:"B", t1:"B1", t2:"B6"},
  {day:1, time:"14:44", field:"Field A", group:"A", t1:"A3", t2:"A4"},
  {day:1, time:"14:44", field:"Field B", group:"A", t1:"A7", t2:"A5"},
  {day:1, time:"14:44", field:"Field C", group:"B", t1:"B4", t2:"B7"},
  {day:1, time:"14:44", field:"Field D", group:"B", t1:"B3", t2:"B6"},
  {day:1, time:"15:12", field:"Field A", group:"A", t1:"A3", t2:"A6"},
  {day:1, time:"15:12", field:"Field B", group:"A", t1:"A1", t2:"A5"},
  {day:1, time:"15:12", field:"Field C", group:"A", t1:"A4", t2:"A7"},
  {day:1, time:"15:40", field:"Field B", group:"A", t1:"A1", t2:"A2"},
  {day:1, time:"15:40", field:"Field C", group:"B", t1:"B3", t2:"B7"},
  {day:1, time:"15:40", field:"Field D", group:"B", t1:"B2", t2:"B6"},
  {day:1, time:"16:08", field:"Field A", group:"A", t1:"A1", t2:"A7"},
  {day:1, time:"16:08", field:"Field B", group:"B", t1:"B1", t2:"B5"},
  {day:1, time:"16:08", field:"Field C", group:"B", t1:"B3", t2:"B4"},
  {day:1, time:"16:08", field:"Field D", group:"B", t1:"B6", t2:"B7"},
  {day:2, time:"13:15", field:"Field A", match:"A1 vs B2", round:"Semifinal 1"},
  {day:2, time:"13:15", field:"Field B", match:"B1 vs A2", round:"Semifinal 2"},
  {day:2, time:"13:50", field:"Field A", match:"SF1L vs SF2L", round:"3rd Place"},
  {day:2, time:"14:29", field:"Field A", match:"SF1W vs SF2W", round:"Final"},
];
```

Replace **`BB_SLOTS`** entirely with:
```js
const BB_SLOTS = [
  {day:1, time:"17:00", court:"Gym 2", t1:"McMaster University", t2:"Sharomani Akaali Dal"},
  {day:1, time:"17:00", court:"Gym 3", t1:"Icemen", t2:"Brockish"},
  {day:1, time:"17:30", court:"Gym 2", t1:"Sher Elite", t2:"Akaal Fauj"},
  {day:1, time:"18:00", court:"Gym 2", t1:"Kaurs United FC", t2:"Panjab Lions"},
  {day:1, time:"18:00", court:"Gym 3", t1:"Farmers Elite", t2:"Dixie United"},
  {day:1, time:"18:30", court:"Gym 2", t1:"Brockish", t2:"NPSS"},
  {day:1, time:"18:30", court:"Gym 3", t1:"Sher Elite", t2:"McMaster University"},
  {day:1, time:"19:00", court:"Gym 2", t1:"Dixie United", t2:"Kaurs United FC"},
  {day:1, time:"19:00", court:"Gym 3", t1:"Babbar Khalsa Ballers", t2:"KLF"},
  {day:1, time:"19:30", court:"Gym 2", t1:"NPSS", t2:"Sharomani Akaali Dal"},
  {day:1, time:"19:30", court:"Gym 3", t1:"KGA", t2:"Brockish"},
  {day:1, time:"20:00", court:"Gym 2", t1:"Babbar Khalsa Ballers", t2:"McMaster University"},
  {day:1, time:"20:00", court:"Gym 3", t1:"KLF", t2:"Panjab Lions"},
  {day:1, time:"20:30", court:"Gym 2", t1:"Kaurs United FC", t2:"Farmers Elite"},
  {day:1, time:"20:30", court:"Gym 3", t1:"KGA", t2:"Sharomani Akaali Dal"},
  {day:1, time:"21:00", court:"Gym 2", t1:"KLF", t2:"Icemen"},
  {day:1, time:"21:00", court:"Gym 3", t1:"NPSS", t2:"Akaal Fauj"},
  {day:1, time:"21:30", court:"Gym 2", t1:"KGA", t2:"Dixie United"},
  {day:1, time:"21:30", court:"Gym 3", t1:"Babbar Khalsa Ballers", t2:"Sher Elite"},
  {day:1, time:"22:00", court:"Gym 2", t1:"Panjab Lions", t2:"Icemen"},
  {day:1, time:"22:00", court:"Gym 3", t1:"Akaal Fauj", t2:"Farmers Elite"},
  {day:2, time:"15:30", court:"Gym 2", match:"BB #1 vs #8", round:"Quarterfinal 1", slotMin:20},
  {day:2, time:"15:30", court:"Gym 3", match:"BB #4 vs #5", round:"Quarterfinal 2", slotMin:20},
  {day:2, time:"15:55", court:"Gym 2", match:"BB #2 vs #7", round:"Quarterfinal 3", slotMin:20},
  {day:2, time:"15:55", court:"Gym 3", match:"BB #3 vs #6", round:"Quarterfinal 4", slotMin:20},
  {day:2, time:"16:20", court:"Gym 2", match:"QF1W vs QF2W", round:"Semifinal 1", slotMin:20},
  {day:2, time:"16:20", court:"Gym 3", match:"QF3W vs QF4W", round:"Semifinal 2", slotMin:20},
  {day:2, time:"16:45", court:"Gym 2", match:"BBSF1L vs BBSF2L", round:"3rd Place", slotMin:20},
  {day:2, time:"17:40", court:"Gym 2", match:"BBSF1W vs BBSF2W", round:"Final", slotMin:30},
];
```

Replace **`VB_SLOTS`** entirely with:
```js
const VB_SLOTS = [
  {day:1, time:"17:00", court:"Gym 1", t1:"Dixie United", t2:"Kaurs United FC"},
  {day:1, time:"17:20", court:"Gym 1", t1:"Panjab Lions", t2:"KLF"},
  {day:1, time:"17:40", court:"Gym 1", t1:"Brockish", t2:"McMaster University"},
  {day:1, time:"18:00", court:"Gym 1", t1:"Sharomani Akaali Dal", t2:"Babbar Khalsa Ballers"},
  {day:1, time:"18:20", court:"Gym 1", t1:"Baba Bidhi Chand Chinna VC", t2:"KLF"},
  {day:1, time:"18:40", court:"Gym 1", t1:"Sharomani Akaali Dal", t2:"Brampton Spikers"},
  {day:1, time:"19:00", court:"Gym 1", t1:"Panjab Lions", t2:"Vortex"},
  {day:1, time:"19:20", court:"Gym 1", t1:"Waymakers", t2:"McMaster University"},
  {day:1, time:"19:40", court:"Gym 1", t1:"Palm Springs", t2:"Kaurs United FC"},
  {day:1, time:"20:00", court:"Gym 1", t1:"Palm Springs", t2:"Dixie United"},
  {day:1, time:"20:20", court:"Gym 1", t1:"Waymakers", t2:"Brockish"},
  {day:1, time:"20:40", court:"Gym 1", t1:"Chinguacousy Ekta", t2:"Babbar Khalsa Ballers"},
  {day:1, time:"21:00", court:"Gym 1", t1:"Chinguacousy Ekta", t2:"Brampton Spikers"},
  {day:1, time:"21:20", court:"Gym 1", t1:"Baba Bidhi Chand Chinna VC", t2:"Vortex"},
  {day:2, time:"15:30", court:"Gym 1", match:"VBSF1 vs VBSF4", round:"Semifinal 1", slotMin:20},
  {day:2, time:"15:50", court:"Gym 1", match:"VBSF2 vs VBSF3", round:"Semifinal 2", slotMin:20},
  {day:2, time:"16:15", court:"Gym 1", match:"VBSF1L vs VBSF2L", round:"3rd Place", slotMin:20},
  {day:2, time:"17:10", court:"Gym 1", match:"VBSF1W vs VBSF2W", round:"Final", slotMin:26},
];
```
Notes: soccer group games use the existing `A#`/`B#` group tokens (resolved via `DEFAULT_SOCCER_GROUPS`) and carry `group:'A'|'B'`. Knockout games use the token conventions already in the file (`A1 vs B2`, `SF1W`, `BBSF1`, `VBSF1`, etc.). Per-game `slotMin` overrides are included where a game differs from the sport default (basketball final 30 min; volleyball Sunday semis/bronze 20 min, final 26 min).

## PHASE 3 — Standings & DQ
- **Standings ranking:** Soccer group = points (3 win / 1 draw / 0 loss) → goal difference → goals scored. Basketball & volleyball = wins → score differential (points for − against). Basketball advances TOP 8; volleyball TOP 4. Recompute live on every Firebase update.
- **Advancement:** Soccer top 2 of each group → cross-over semis (A1 v B2, B1 v A2). Basketball TOP 8 → quarterfinals (QF1 #1v#8, QF2 #4v#5, QF3 #2v#7, QF4 #3v#6) → semis → bronze → final. Volleyball TOP 4 → SF1 (#1v#4), SF2 (#2v#3) → bronze → final.
- **DQ / no-show (basketball):** mark a team disqualified → opponent gets a win and **+10 score differential**. Confirm the exact scoreline representation. (Ask whether volleyball DQ uses the same rule.)

## PHASE 4 — Live TV standings screens
Ensure hash-addressable, auto-updating TV screens exist for: `#tv/standings-soccer` (both groups), `#tv/standings-basketball`, `#tv/standings-volleyball`, styled to match the existing broadcast look, added to the TV picker + rotating carousel.

## PHASE 5 — Format facts (for any info/labels shown)
- **Soccer:** 20-min games, no timeouts (halftime only), 4 fields, max 3 games back-to-back per team, Rebel FC all games on Field D. Sunday knockout 1:15–2:51 PM (buffer after 3rd-place).
- **Basketball:** 20-min games (final 30 min), 1 timeout/team, Gym 2 & Gym 3. Sunday TOP-8 knockout 3:30 PM (quarters) → Final 5:40–6:10 PM (played alone). Interleaved with volleyball.
- **Volleyball:** Gym 1 only. Sat 15-min games; Sunday semis/bronze 20-min; **Final = best of 3 sets × 8 min, 1-min break between sets**. 1 timeout/team (inside game). Sunday TOP-4 playoff interleaved with basketball: semis 3:30/3:50, bronze 4:15, Final 5:10–5:36 PM (played alone). Both finals never overlap; everything ends 6:10 PM.

## Verify before done (all must pass)
1. **Guaranteed Saturday games:** soccer 6/team, basketball 3/team, volleyball 2/team. Print per-team counts (soccer 14 teams, basketball 14, volleyball 14).
2. **No cross-sport overlap:** none of the 8 dual-sport teams (Babbar Khalsa Ballers, KLF, Brockish, McMaster University, Dixie United, Kaurs United FC, Sharomani Akaali Dal, Panjab Lions) has a Saturday basketball game overlapping a volleyball game; each gets ≥20-min rest.
3. No team double-booked; no field/court clash in any slot.
4. **Soccer:** no team plays more than 3 games back-to-back; Rebel FC all games on **Field D**, first game **12:52 PM**; group stage finishes **4:28 PM**.
5. **Seeded matchups intact (so the best teams advance):** basketball top-4 seeds (KLF, KGA, Babbar Khalsa Ballers, Sher Elite) never play each other — note: Babbar Khalsa Ballers plays two marquee games (KLF and Sher Elite) plus McMaster University, all after 7 PM (requested exception to the top-4-protection). Volleyball is 14 teams incl. **Waymakers**; top-4 seeds (Chinguacousy Ekta, Baba Bidhi Chand Chinna VC, Waymakers, Palm Springs) never play each other — and Chinguacousy Ekta is never scheduled against Baba Bidhi Chand Chinna VC.
6. **Sunday structure:** basketball is a **TOP-8** bracket (4 quarterfinals → 2 semis → bronze → final); volleyball is **TOP-4**. The basketball final (5:40 PM) and volleyball final (5:10 PM) do **NOT** overlap, and everything ends by **6:10 PM**.
7. `index.html`, `admin.html`, `KO-2026-Brackers/index.html` have identical constants + slot arrays.

Show diffs per file and run checks 1–6 before declaring done. Ask the DQ confirm question before Phase 3.
