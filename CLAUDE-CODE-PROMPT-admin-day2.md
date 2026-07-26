# Claude Code Prompt — Update the ADMIN (scorekeeper) page for the Day-2 knockout

## Context (read first)
`admin.html` is the PIN-locked scorekeeper that writes scores to Firebase. We've finalized the Day-1 standings and locked the Day-2 knockout seeds (see the `finalize-day2` / `finalize-basketball` prompts). Now the **admin page needs to run the Day-2 knockout**: the scorekeeper enters each round's results and the bracket **auto-advances winners into the next round — so the semifinals (and then the finals) get decided right there in admin**, and the public dashboard updates live.

## Files
Primary: `admin.html`. Keep the locked seeds/standings **identical** to `index.html` and `KO-2026-Brackers/index.html` (do not diverge). Preserve Firebase sync, the PIN lock, live score entry, and existing soccer/volleyball/basketball scoring.

## PHASE 1 — Analyze, then pause
In `admin.html` map: how a game score is entered and written to Firebase; how (or whether) the Day-2 knockout currently renders in admin; how winners/losers are determined (including ties — note basketball/volleyball have no draws, soccer knockout may need a tiebreak); the schema-version constant. Report a short plan and **pause for review**.

## PHASE 2 — Load the locked seeds into admin
Mirror the same locked Day-2 matchups already set on the public dashboard so admin shows the correct opening-round games:

**🏀 Basketball TOP-8 (Gym 2 & 3):** QF1 KGA vs Brockish · QF2 Sher Elite vs Dixie United · QF3 KLF vs Kaurs United FC · QF4 Akaal Fauj vs Babbar Khalsa Ballers.
**⚽ Soccer (Fields A/B):** SF1 KLF vs Mohali FC · SF2 Rebel FC vs Humberwood.
**🏐 Volleyball (Gym 1):** SF1 Waymakers vs Chinguacousy Ekta · SF2 Palm Springs vs Baba Bidhi Chand Chinna VC.

## PHASE 3 — Make the knockout ADVANCE as results are entered
When the scorekeeper enters a result in admin, the winner (and loser) must flow to the next round automatically and write to Firebase:

- **Basketball:** entering **QF1–QF4** winners fills **Semifinal 1 = QF1 W vs QF2 W** and **Semifinal 2 = QF3 W vs QF4 W**. Entering the two **semifinal** results fills **3rd Place = SF losers** and **Final = SF winners**.
- **Soccer & Volleyball:** entering the two **semifinal** results fills **3rd Place = SF losers** and **Final = SF winners**.
- The advanced team names must appear in the next round's cards in **both** admin and the public dashboard (via Firebase), with no manual re-entry.
- Keep DQ/no-show handling consistent with the rest of the app (basketball no-show = opponent wins +10 differential); ask me before applying any DQ rule to volleyball.

Bump the schema-version constant so cached clients rebuild.

## PHASE 4 — Verify (do these before declaring done)
1. Enter sample basketball QF results → **Semifinal 1 and Semifinal 2 auto-populate** with the correct winners.
2. Enter the basketball semifinal results → **Final and 3rd-place auto-populate**; repeat for soccer and volleyball semifinals.
3. Confirm the public dashboard (`index.html`) reflects each advancement **live** via Firebase (no refresh).
4. Locked Day-1 standings and opening-round matchups are unchanged; PIN lock, mobile layout, and existing scoring still work.
5. `admin.html`, `index.html`, `KO-2026-Brackers/index.html` share identical seed data; schema bumped.

Show diffs and run checks 1–3 before declaring done. Pause for review after Phase 1.
