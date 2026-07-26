# Claude Code Prompt — Finalize SOCCER standings & knockout (index + admin)

## Context (read first)
Some Saturday soccer games were **deleted from Firebase**, so live-computed soccer standings are unreliable. Lock the **FINAL soccer group standings** and the **knockout matchups** to match the official end-of-Day-1 screenshots below, on **both the public dashboard and the admin scorekeeper**, and make the admin advance the semifinals into the final.

**Scope rule:** change **SOCCER only**. Do not touch basketball or volleyball (basketball is already finalized). Preserve Firebase sync, the admin PIN lock, live scoring, mobile layout, and TV modes.

## Files (keep all three identical)
`index.html` (public), `admin.html` (scorekeeper), `KO-2026-Brackers/index.html` (secondary copy).

## PHASE 1 — Analyze, then pause
Find, for soccer: how group standings are computed/rendered (points → goal difference → goals for), how the knockout resolves seeds (`A1`, `B2`, `SF1W`, etc.), how the admin enters soccer scores and writes to Firebase, the schema-version constant. Report a short plan and **pause for review**.

## PHASE 2 — Lock the FINAL soccer group standings
Add a hardcoded `SOCCER_FINAL_STANDINGS` (per group, order = final rank) used to render the tables **and** resolve knockout seeds instead of recomputing from live scores. Bump the schema-version constant. Ranking rule: **points → goal difference → goals scored**; top 2 of each group advance.

**Group A (final):**
| # | Team | P | W | D | L | GF | GA | GD | Pts | |
|---|------|---|---|---|---|----|----|----|-----|--|
| 1 | KLF | 6 | 5 | 0 | 1 | 18 | 8 | +10 | 15 | ADV |
| 2 | Humberwood | 6 | 4 | 2 | 0 | 24 | 7 | +17 | 14 | ADV |
| 3 | Brockish | 6 | 4 | 1 | 1 | 19 | 6 | +13 | 13 | |
| 4 | NPSS | 6 | 3 | 1 | 2 | 14 | 12 | +2 | 10 | |
| 5 | Babbar Khalsa Ballers | 6 | 2 | 0 | 4 | 14 | 23 | −9 | 6 | |
| 6 | Sharomani Akaali Dal | 6 | 1 | 0 | 5 | 5 | 36 | −31 | 3 | |
| 7 | Panjab Lions | 6 | 0 | 0 | 6 | 0 | 2 | −2 | −15 | |

**Group B (final ranking):** 1) **Rebel FC** ADV · 2) **Mohali FC** ADV · 3) Punjab Kings · 4) Dixie United · 5) Akaal Fauj · 6) Kaurs United FC · 7) McMaster University.
> ⚠️ I have Group B's **order** but not its full P/W/D/L/GF/GA numbers (they were cut off / affected by deleted games). Keep Group B's per-team stats as currently stored, but force the **rank order above**. If you want the exact Group B stats locked too, ask me and I'll provide them.

## PHASE 3 — Lock the soccer knockout (Fields A & B)
- **SF1 (1:15 PM, Field A):** KLF (A1) vs Mohali FC (B2)
- **SF2 (1:15 PM, Field B):** Rebel FC (B1) vs Humberwood (A2)
- **3rd Place (1:50 PM, Field A):** SF1 Loser vs SF2 Loser
- **Final (2:29 PM, Field A):** SF1 Winner vs SF2 Winner

## PHASE 4 — Admin advancement (decide the final in admin)
In `admin.html`, entering the two soccer **semifinal** results must auto-fill **3rd Place = SF losers** and **Final = SF winners**, and write to Firebase so the public dashboard updates live with no manual re-entry. Soccer knockouts can tie in regulation — keep whatever tiebreak the app already uses (penalties/extra time); if none exists, add a simple winner-select in admin and ask me before changing the rule.

## Verify before done
1. Group A renders exactly as the table above; Group B shows the rank order above (Rebel FC #1, Mohali FC #2).
2. Knockout shows **KLF vs Mohali FC** and **Rebel FC vs Humberwood** at 1:15 PM on both index and admin.
3. Entering the two semifinal results in admin auto-fills the final and 3rd-place, and the public dashboard updates live.
4. Deleting/editing a Day-1 soccer score no longer changes the locked standings or bracket.
5. Basketball and volleyball are unchanged. `index.html`, `admin.html`, `KO-2026-Brackers/index.html` are identical; schema bumped.

Show diffs per file and run checks 1–4 before declaring done. Pause after Phase 1.
