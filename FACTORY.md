# FACTORY.md — The Ian Labs Constitution
Version 0.1 · Amended only through weekly retros (Loop 4)

## Roles
- **Designer:** daemon. Writes specs, approves plans, presses the
  production button. The only human on the floor.
- **Stations:** Planner, Builder, Reviewer, Watchtower — agent roles
  defined in `/factory/prompts/`. No station may act outside its prompt.

## The Ten Rules

1. **Tests ship with features.** Every feature arrives with tests.
   No tests, no merge.
2. **No secrets, ever.** No secrets in code, commits, or logs.
   Environment variable *names* may be documented; values never
   leave encrypted storage.
3. **One task = one branch = one merge.** A builder touches only
   files inside its assigned task's scope.
4. **Dependencies are justified.** No new dependency without a
   one-line written justification.
5. **Main is protected.** Nothing lands on `main` without passing its
   gate profile. No force-pushes to `main` — by anyone, including
   the Designer.
6. **Claims require evidence.** "Done" means a passing test run, a
   green check, or a screenshot — shown, not asserted. Claims
   without evidence are treated as failures.
7. **Loops are capped.** Every agent-to-agent loop stops at 3 cycles,
   then escalates to the Designer with a one-paragraph summary of
   the disagreement.
8. **Humans press the big buttons.** Production deploys, payments,
   user data, and messages to real people require the Designer's
   explicit approval — at every autonomy level, forever.
9. **Money has a ceiling.** Monthly API budget: $20. Alert at 80%.
   Hard stop at 100%.
10. **The factory learns weekly.** A retro happens every week the
    factory operates. Lessons are amended into this file — that
    amendment is the factory improving itself.

## Gate profiles
| Profile | Requirements |
|---|---|
| `prototype` | tests pass + secret scan clean |
| `standard` | prototype + lint + Reviewer (Gate B) approval |
| `strict` | standard + Designer review + higher coverage bar |

## Amendments log
- v0.1 — 2026-07-08 — Constitution adopted.
