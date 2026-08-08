# Eval results — <YYYY-MM-DD>

**Skill version:** <git SHA or "uncommitted">
**Model:** <e.g. claude-opus-5>
**Session:** fresh / resumed
**Activation:** `/style auto`
**Confounds:** <SessionStart hook active? outputStyle set? note them, or "none">

## Scores

R = route, S = substance, U = uncertainty, V = verbatim, B = brevity.
Pass = all five at 1.

| Case | Expected route | Actual route | R | S | U | V | B | Pass |
|---|---|---|---|---|---|---|---|---|
| E1 trade-off survival | Hemingway | | | | | | | |
| E2 causal chain | Hemingway | | | | | | | |
| E3a status | Caveman + next action | | | | | | | |
| E3b status w/ substance | Hemingway | | | | | | | |
| E4 uncertainty under STE | STE + ADHD | | | | | | | |
| E5 list cap vs substance | Hemingway | | | | | | | |
| E6 safety override | Safety Override | | | | | | | |
| E7 verbatim layer | ADHD + Hemingway | | | | | | | |
| E8 depth on request | Hemingway, long form | | | | | | | |
| E10 persisted text exempt | Normal prose | | | | | | | |

**E9 (no style leakage)** applies to all replies: pass / fail — <which reply leaked>

**Total: <n>/10**

## Failures

For each failed case:

### <Case ID> — <axis that failed>

- **Missing:** <the exact item that should have been on the page>
- **Reply said:** <quote>
- **Root cause:** <which rule, in which file, permitted this>
- **Proposed fix:** <file:section, and the change>

## Surprises

Anything the router did that was not wrong but was not expected. Style chosen
over the predicted one, a blend that read badly, a rule that fired too often.

## Verdict

- [ ] Ship as is
- [ ] Ship with fixes listed above
- [ ] Needs rework — <what>

## Regression note

E4 and E6 are the router-specific cases. Re-run both after any edit to
`SKILL.md`, `styles/ste.md`, or `styles/caveman.md`.
