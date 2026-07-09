# SPEC: Gig-Pricing Calculator

**Gate profile:** standard
**Designer:** daemon · **Date:** 2026-07-09

## Goal
A single-page web tool that turns daemon's gut pricing process into a
consistent, repeatable formula. The user enters 3–5 competitor prices,
picks a complexity tier, and toggles rush delivery; the tool returns one
recommended price (charm-rounded, floor-protected) with a one-line
rationale explaining how it was computed. When this is done, pricing a
new gig takes under a minute and two identical inputs always produce
identical prices.

## Non-goals
- No accounts, login, or user profiles.
- No fetching data from Fiverr or any external site/API — every input is
  typed by the user. (Automated data gathering is Product #2's job; this
  calculator is the brain, not the hands.)
- No saving or history of past calculations.
- No currency conversion — USD only.
- No tiered packages (basic/standard/premium) — single price only. v2 candidate.
- No public deployment — runs locally (localhost) for the pilot.
- No fancy design — a plain form and a result line is complete.

## Core scenario
As daemon pricing a new Fiverr gig, I open the page on localhost, type in
the prices of 3–5 competitor gigs I just looked up, choose the complexity
tier (considering scope and technical difficulty), tick "rush" if the
buyer needs fast delivery, and click Calculate. I get one recommended
price plus a one-line rationale, in under a minute, and I trust it because
it prices exactly the way I would — just consistently.

## Pricing model (the formula — Designer's judgment, encoded)
Deterministic pipeline, applied in this exact order:

1. **Median** of the entered competitor prices (3–5 values accepted).
2. **Positioning:** base = median × 0.90 — i.e. 10% below median.
   [PROPOSED: 10%. Veto or confirm.]
3. **Complexity multiplier** (one tier, chosen by the user; guidance text
   on the form: "consider scope + technical difficulty"):
   - simple ×0.85 · standard ×1.00 · complex ×1.30
   [PROPOSED multipliers. Veto or confirm.]
   Technical difficulty lives HERE and only here — it is never a
   separate booster (no double counting).
4. **Rush booster:** if rush is ticked, price × 1.25.
   [PROPOSED: +25%. Veto or confirm.]
5. **Charm rounding:** round to the nearest multiple of 5; if the result
   is a multiple of 10, subtract 1 (so outputs end in 5 or 9 — the
   attractive, message-triggering price points).
   Examples: 117 → 115 · 72 → 69 · 61.20 → 59.
   [PROPOSED rule. Veto or confirm.]
6. **Floor check:** final = max(charm price, floor).
   Floor = $50  [SET by daemon, 2026-07-09.]

## Guardrails
- **Stack:** Python 3.x + FastAPI serving one HTML page; pytest for
  tests. No database. No JavaScript framework. No external API calls.
- **Budget ceiling:** $0 infrastructure (localhost only). Build-time API
  spend falls under FACTORY.md Rule 9's monthly cap.
- **Security / data rules:** no user data stored anywhere; no secrets
  needed or permitted; nothing leaves the machine.

## Acceptance criteria
Each individually checkable by an automated test or a 2-minute manual check.

1. Given competitor prices [60, 70, 80, 90, 100], the computed median is
   80; given [60, 70, 80, 90], it is 75 (even-count median = mean of the
   two middle values).
2. Base price = median × 0.90. Test: median 80 → base 72.00.
3. Complexity multiplier is applied to base: simple → ×0.85, standard →
   ×1.00, complex → ×1.30. Test: base 72 complex → 93.60.
4. Rush ticked applies ×1.25 after complexity. Test: 93.60 → 117.00.
   Rush unticked changes nothing.
5. Charm rounding follows the exact rule in the pricing model.
   Tests: 117 → 115 · 72 → 69 · 61.20 → 59.
6. Final price is never below the floor: with floor $F, any computed
   price under $F returns exactly $F. Test with a computed price below F.
7. The result includes a one-line rationale naming: the median, the
   positioning, the complexity tier, and rush status. Example:
   "Median $80, positioned 10% below, complex ×1.3, rush +25%,
   charm-rounded → $115."
8. Invalid input — fewer than 3 prices, more than 5, or any non-numeric
   value — returns a clear error message, shows no price, and does not
   crash.
9. The full core scenario works end-to-end in a browser via the form:
   enter prices → choose tier → toggle rush → Calculate → see price +
   rationale on the same page.

## Quality gates
FACTORY.md `standard` profile: full pytest suite green + lint + Quality
Gate B (adversarial reviewer) APPROVE, verified per numbered criterion
above. Secret scan is a manual glance at Level 0 (nothing secret should
exist in this repo at all).
