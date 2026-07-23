# KO 2026 — Brand Restyle Notes

Visual restyle of `index.html` (Live Dashboard) and `admin.html` (Scorekeeper)
into the Khalsa Olympics brand. **No behavior, routing, state, or DOM-structure
changes** beyond what the restyle strictly required.

## What was added
- **`styles/brand.css`** — the single shared brand sheet:
  - Full token system from the design handoff (navy/gold/cream scales,
    semantic colors, gradients, halftone, shadows/glows, motion tokens).
  - **Legacy-variable remap:** both pages already themed via CSS custom
    properties (`--navy/--gold/--gray2/…` in index; `--bg/--card/--border/…`
    in admin). brand.css redefines those variables to brand tokens in the same
    `:root`, loaded after each page's inline styles — so nearly every existing
    rule now consumes brand tokens without touching the original CSS. This is
    the lowest-risk path to "refactor existing CSS to consume the tokens."
  - Typography: Barlow body everywhere; Cinzel on ceremonial titles
    (lockup, headers, PIN title, TV header, modal titles); Anton on scores,
    timers, clocks, team names, LIVE labels.
  - Component layers: pill buttons (+hover lift/glow, press scale), 56px+
    score buttons, 64px PIN keys, gold-hairline cards + `--shadow-card`,
    gold diamond dividers, gold focus rings, status pills, test-mode ribbon,
    arena (`--bg-ink-gold`) mood on admin + TV, regal (`--bg-navy-glow`) on
    the public dashboard, ghost-crest watermark behind TV screens and the
    crest on the PIN screen / headers, `prefers-reduced-motion` support.
- **`assets/brand/`** — `crest-color.png`, `crest-watermark-ghost.png`,
  `logo-lockup-color.png` (copied verbatim from the design handoff; not
  redrawn). Note: PNGs are 1.8–3.3 MB; consider optimized/WebP versions later.
- `<head>` additions on both pages: Google Fonts (Cinzel, Cormorant Garamond,
  Anton, Barlow), Font Awesome 6.5.2 (cdnjs), `styles/brand.css`.
- `class="admin-body"` on the admin `<body>` (selects the arena background).

## Emoji → Font Awesome
Swapped in **static chrome**: public nav (Brackets/Schedule/TV), public sport
tabs, all TV-picker links, admin toolbar (View Live/Setup/Lock), admin sport +
mode tabs. Emoji generated inside JS template strings (score cards, event
logs, dynamic buttons) were deliberately left: replacing them means editing
dozens of render functions (behavior-adjacent churn the handoff told us to
avoid). Follow-up candidate if full emoji removal is wanted.

## Copy corrections
- Venue: “Brampton” → **“Paramount Fine Foods Centre, Mississauga”**, plus
  “4th Annual”, in the public header and TV overlay sub-line.
  (The volleyball team “Brampton spikers” is a team name and was not touched.)

## JS touched
None. Zero changes to any `<script>` content.

## Class renames
None — all styling was additive (new stylesheet + head links + icon `<i>`
elements inside existing buttons).

## Verified after restyle
PIN unlock, hash routing (`#dashboard`, `#tv/...`), sport/view switching,
score entry buttons, game timer, standings tables (soccer groups + BB/VB
round-robin), TV picker, rotating carousel — all working; no console errors.

## `KO-2026-Brackers/index.html`
Kept as an exact copy of `index.html` with asset/stylesheet paths adjusted
(`styles/…` → `../styles/…`) since it lives one directory down.

## Design handoff v2 additions
- `assets/brand/ko-logo-primary.png` — THE primary logo (crest + wordmark on
  navy). Now used in both page headers and on the PIN screen (navy surfaces,
  per the handoff). Crest-only asset retained for compact uses.
- Admin event log: Yellow/Red card buttons now show **only for soccer games**
  (small JS addition in `renderEventLog`); basketball/volleyball keep
  Goal + Foul. If a card type was selected when switching sports, selection
  resets to Goal.
- Confirmed: no Tug of War anywhere; venue copy is Mississauga.
