# Claude Code Prompt — Fix the dashboard's BASKETBALL schedule to the final version

## What's wrong now
`index.html` (and `admin.html`) currently have basketball set to **15-minute games, 6 games per team (42 games)** — `const BB_SLOT = 15` and a 42-game `BB_SLOTS`. That is NOT the intended format. The correct, final basketball format is **20-minute games, exactly 3 games per team**, with **no overlaps against volleyball** for dual-sport teams and a Sunday top-4 knockout.

## Scope
Change **basketball only**. Do NOT touch soccer or volleyball (`SOCCER_SLOTS`, `SOC_SLOT`, `VB_SLOTS`, `VB_SLOT`) or their logic. Preserve Firebase sync, TV screens, standings, and mobile layout.

## Files to update (keep all three consistent)
- `index.html`
- `admin.html`  (its `BB_SLOTS` MUST match `index.html` exactly)
- `KO-2026-Brackers/index.html` (secondary copy — keep in sync)

## Changes

### 1) Game length
Set the basketball slot length to 20 minutes:
```js
const BB_SLOT = 20;   // was 15
```

### 2) Replace the entire BB_SLOTS array with this exact schedule
This is the final schedule: 21 round-robin games (3 per team) Saturday 5:00–10:50 PM across Gym 2 & Gym 3, then the Sunday top-4 knockout 3:30–4:50 PM. Team tokens are the existing basketball seeds (`T1`=Sher Elite, `T2`=Babbar Khalsa Ballers, `T3`=Dixie United, `T4`=Sharomani Akaali Dal, `T5`=KLF, `T6`=Akaal Fauj, `T7`=NPSS, `T8`=McMaster University, `T9`=Brockish, `T10`=Farmers Elite, `T11`=Icemen, `T12`=KGA, `T13`=Panjab Lions, `T14`=Kaurs United). Knockout tokens (`BBSF1`…) follow the existing convention (top-4 standings seeds; `L`/`W` = semifinal loser/winner).

```js
const BB_SLOTS = [
  {start:D1(17,00), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 1', desc:'T7 vs T10'},
    {court:'Gym 3 (Basketball)', phase:'rr', round:'RR Game 2', desc:'T9 vs T11'},
  ]},
  {start:D1(17,30), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 3', desc:'T5 vs T12'},
    {court:'Gym 3 (Basketball)', phase:'rr', round:'RR Game 4', desc:'T8 vs T14'},
  ]},
  {start:D1(18,00), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 5', desc:'T4 vs T10'},
    {court:'Gym 3 (Basketball)', phase:'rr', round:'RR Game 6', desc:'T7 vs T11'},
  ]},
  {start:D1(18,30), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 7', desc:'T2 vs T3'},
    {court:'Gym 3 (Basketball)', phase:'rr', round:'RR Game 8', desc:'T1 vs T5'},
  ]},
  {start:D1(19,00), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 9', desc:'T4 vs T9'},
    {court:'Gym 3 (Basketball)', phase:'rr', round:'RR Game 10', desc:'T8 vs T13'},
  ]},
  {start:D1(19,30), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 11', desc:'T1 vs T6'},
    {court:'Gym 3 (Basketball)', phase:'rr', round:'RR Game 12', desc:'T2 vs T14'},
  ]},
  {start:D1(20,00), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 13', desc:'T3 vs T4'},
  ]},
  {start:D1(20,30), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 14', desc:'T12 vs T13'},
  ]},
  {start:D1(21,00), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 15', desc:'T5 vs T14'},
    {court:'Gym 3 (Basketball)', phase:'rr', round:'RR Game 16', desc:'T6 vs T10'},
  ]},
  {start:D1(21,30), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 17', desc:'T1 vs T2'},
    {court:'Gym 3 (Basketball)', phase:'rr', round:'RR Game 18', desc:'T11 vs T13'},
  ]},
  {start:D1(22,00), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 19', desc:'T9 vs T12'},
    {court:'Gym 3 (Basketball)', phase:'rr', round:'RR Game 20', desc:'T3 vs T7'},
  ]},
  {start:D1(22,30), rows:[
    {court:'Gym 2 (Basketball)', phase:'rr', round:'RR Game 21', desc:'T6 vs T8'},
  ]},
  {start:D2(15,30), rows:[
    {court:'Gym 2 (Basketball)', phase:'ko', round:'Semifinal 1', desc:'BBSF1 vs BBSF4', slotMin:20},
    {court:'Gym 3 (Basketball)', phase:'ko', round:'Semifinal 2', desc:'BBSF2 vs BBSF3', slotMin:20},
  ]},
  {start:D2(16,00), rows:[
    {court:'Gym 2 (Basketball)', phase:'ko', round:'3rd Place', desc:'BBSF1L vs BBSF2L', slotMin:20},
  ]},
  {start:D2(16,30), rows:[
    {court:'Gym 2 (Basketball)', phase:'ko', round:'Final', desc:'BBSF1W vs BBSF2W', slotMin:20},
  ]},
];
```

### 3) Bump the schema/version constant
If there is a basketball schema-version constant (e.g. `BB_SCHEMA_V`) or a global state version, increment it so cached client state rebuilds from the new games instead of showing stale 42-game data.

## Verify before finishing (all must pass)
1. **Each basketball team plays exactly 3 games** in the Saturday round-robin (14 teams × 3 = 21 games). Print a per-team count to confirm.
2. **No dual-sport overlap:** for every team that plays both basketball and volleyball (Babbar Khalsa Ballers, Dixie United, Sharomani Akaali Dal, KLF, McMaster University, Brockish, Panjab Lions, Kaurs United), no basketball game's time window overlaps any of its volleyball game windows. (Basketball games are 20 min; volleyball 15 min. Confirm zero overlaps.)
3. Basketball games render as **20-minute** games and the Saturday card ends by ~10:50 PM; Sunday knockout is 3:30/3:30/4:00/4:30 (SF1, SF2, 3rd, Final).
4. Soccer and volleyball schedules are **unchanged**.
5. `index.html`, `admin.html`, and `KO-2026-Brackers/index.html` all have identical `BB_SLOT` and `BB_SLOTS`.

Do the analysis/read first, show me the diff for each file, and run the two verification checks (per-team game count + volleyball overlap) before declaring done.
