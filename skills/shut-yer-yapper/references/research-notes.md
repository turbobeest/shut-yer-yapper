# Research Notes

Sources examined, rules extracted, conflicts found, and how the Reasoning
Sanctity Invariant resolves each one.

All repos cloned at depth 1 on 2026-08-07. Commit SHAs are the tip of the
default branch at that time.

## Sources

| Style | Repo | Commit | Last push | Stars | File read |
|---|---|---|---|---|---|
| Caveman | `JuliusBrussee/caveman` | `14d4f2e` | 2026-08-08 | 96,708 | `skills/caveman/SKILL.md`, `skills/caveman-commit/SKILL.md`, `skills/caveman-compress/SKILL.md`, `plugins/caveman/skills/cavecrew/SKILL.md`, `docs/HONEST-NUMBERS.md` |
| ADHD | `ayghri/i-have-adhd` | `2d19ad2` | 2026-08-06 | 18,159 | `skills/i-have-adhd/SKILL.md` |
| STE (primary) | `AminBlg/SimpleEnglish` | `59bf670` | 2026-08-07 | 1,951 | `skills/simple-english/SKILL.md` |
| STE (secondary) | `danyuchn/asd-ste100-skill` | `8564f89` | 2026-07-20 | 541 | `SKILL.md` |
| STE (evaluated) | `TheAngryByrd/simplified-technical-english-skill` | `5d2c32d` | 2026-07-31 | 22 | `skills/simplified-technical-english/SKILL.md` |
| STE (evaluated) | `nuelcyoung/asd-ste100` | `57d02f8` | 2026-07-19 | 14 | `SKILL.md` |
| Hemingway | No canonical repo | — | — | — | Synthesized from classic principles; cross-checked against the anti-slop tables in the Caveman and SimpleEnglish repos |

### STE candidate selection

`AminBlg/SimpleEnglish` was chosen as primary. It is the only candidate that:

- Maps all 53 Issue 9 rules to explicit numbers with software examples
- Ships an **Untouchables** section (code, paths, quoted errors) — directly
  reusable as the router's Layer 3
- Ships a slop-substitution table that is style-agnostic
- Warns, correctly, that models hallucinate STE rule numbers from memory
- Separates pragmatic from strict mode, which maps cleanly onto light/full STE

`danyuchn/asd-ste100-skill` contributed the framing that the *reader may be
another agent or a downstream system*, which is why the router routes agent
handoffs and tool descriptions to STE. Its "flag the trade-off instead of
silently simplifying" rule is the closest thing in the source material to the
Reasoning Sanctity Invariant and was a direct input to it.

`TheAngryByrd` and `nuelcyoung` are faithful but add nothing the primary lacks;
`nuelcyoung` bans `can` alongside the other modals, which is stricter than Issue 9
as encoded elsewhere and would be actively harmful in assessment text.

Hemingway has no dominant skill repo. It is synthesized from the classic
principles — short declarative sentences, strong verbs, concrete nouns, minimal
modifiers, active voice, the iceberg principle — with the iceberg deliberately
bounded so it cannot be read as license to omit substance.

## Extracted rule sets (minimal effective)

**Caveman:** drop articles / filler / pleasantries / hedging; fragments allowed;
short synonyms; no tool narration; no decorative formatting; intensity ladder
(lite / full / ultra); hard preserves (negations, numbers, code, errors);
no invented abbreviations; no arrows; auto-clarity escape for safety and
ambiguity; persisted text exempt.

**ADHD:** ten rules — next action first; number multi-step; end with one action;
suppress tangents; restate state; specific time estimates; make wins visible;
matter-of-fact errors; cap lists at five; no preamble/recap/closer. Plus six
documented break conditions and a five-item pre-send deletion list.

**STE:** classify procedural vs descriptive; one word one meaning one part of
speech; active voice; simple tenses; one instruction per sentence; condition
before command; 20/25-word caps; three-word max noun clusters; no ellipsis;
no semicolons; safety pattern (risk level, command, consequence); untouchables.

**Hemingway:** one idea per sentence; vary sentence length; strong verbs and
concrete nouns; cut factless modifiers; active voice with a named actor; no
throat-clearing; bounded iceberg; say the hard thing plainly.

## Conflicts and resolutions

### C1 — Word omission (hard contradiction)

Caveman: "Drop: articles (a/an/the) ... Fragments OK."
STE Rule 4.2: "Do not omit words or use contractions to shorten sentences. Keep
articles, keep 'that'." The source spells the conflict out itself:
*"Wrong shortening: Ensure file exists before running. STE: Make sure that the
file exists before you run the command."*

**Resolution:** these styles cannot be blended at sentence level. Encoded in
`SKILL.md` as a hard rule and in the blend conflict table. Caveman+STE is not an
offered blend.

### C2 — Synonym policy

Caveman: prefer short synonyms ("big" not "extensive").
STE Rules 1.11 / 9.4: one item, one name; consistent terminology throughout.

**Resolution:** soft conflict. Terminology consistency wins in every blend, and
one-term-per-concept is promoted to a Universal Rule in `SKILL.md`. Caveman's
short-synonym rule applies only to non-technical words, and only in pure Caveman.

### C3 — Modals versus honesty (the critical one)

STE modal ladder: `may / might / could` → `can`; `should` → `must` or delete.
ADHD pre-send check 4: "Keep a hedge that carries real uncertainty; deleting it
manufactures confidence."

These are irreconcilable as written. STE assumes an author documenting a known
procedure. That assumption fails when a model is reasoning about an unfamiliar
system, where the hedge is often the most informative token in the sentence.

**Resolution:** the ladder applies to **instructions** and is suspended for
**assessments**. Promoted to Enforcement Clause 3 of the Reasoning Sanctity
Invariant, because it is the single mechanism by which a style skill can make
the model's output actively wrong rather than merely terse.

### C4 — List caps versus substance

ADHD Rule 9 caps lists at five items.

**Resolution:** the cap governs *action* lists. It never governs findings,
options, or trade-offs that are themselves the answer. ADHD's own break-rule 5
already says the task wins when a rule would delete the answer; this makes it
explicit. Encoded in `styles/adhd.md` Rule 9 and in the blend conflict table.

### C5 — ADHD internal tension (Rule 5 versus Rule 10)

Rule 5 requires restating state every turn. Rule 10 forbids recaps.

**Resolution:** restate *position* ("step 3 of 5 done"), never *content* already
delivered. Where the harness has a task or plan tool, the checklist does the
restating and the prose does not repeat it — the source repo says this too.

### C6 — Sentence-length caps versus rhythm

STE's 20/25-word caps versus Hemingway's requirement to vary sentence length.
All-short prose reads as machine output and hides relationships between clauses.

**Resolution:** light STE, the default in blends, drops the word caps and keeps
the structural rules. Full caps apply only when the user names STE or compliance,
or for genuine safety procedures.

### C7 — Tool-call narration versus harness requirements

Caveman forbids preamble before tool calls. ADHD forbids "Let me..." openers.
Both source repos also concede that the harness system prompt outranks the style.

**Resolution:** the harness always wins. Encoded as ADHD break-rule 6 and in the
blend conflict table. The style constrains the prose the harness leaves to you.

### C8 — Convergent rules (no conflict, promoted to universal)

All three source styles independently arrive at the same escape hatch for safety.
Caveman calls it Auto-Clarity, ADHD calls it break-rule 2, STE devotes Section 7
to it. Unified as the **Safety Override**.

Likewise, all three protect code, paths, commands, and error strings verbatim.
Unified as **Layer 3**.

Caveman's empirical findings — that invented abbreviations (`cfg`, `impl`) and
causal arrows (`→`) save zero tokens under the tokenizer while costing decode
clarity — are style-independent and were promoted to Universal Rules.

## How reasoning protection was applied

The invariant was not bolted on. It changed four concrete decisions:

1. **Default style.** Hemingway is the default specifically because it is the
   only one of the four with no rule capable of deleting a fact. Caveman deletes
   words, STE deletes hedges and caps length, ADHD caps lists. Hemingway only
   constrains sentence construction.
2. **Routing order.** The PRD's original axis order tested for structure first.
   The reasoning-load test was moved to position 2, ahead of every stylistic
   signal, so no formatting preference can outrank depth.
3. **Caveman gating.** Caveman is behind an explicit eligibility gate rather
   than being one option among four. A reply that would say "because" is
   ineligible regardless of length. The `styles/caveman.md` sharding example
   documents the exact failure mode.
4. **STE modal suspension.** See C3. Without it, routing safety-critical or
   agent-facing text to STE would have systematically stripped uncertainty
   markers from exactly the text where uncertainty matters most.

## Cost note

`JuliusBrussee/caveman`'s `docs/HONEST-NUMBERS.md` reports the skill cuts output
tokens ~65% on verbose replies (range 22–87%) while adding ~1–1.5k input tokens
per turn and reducing input by 0%; on terse Q&A it is net-negative. Two design
consequences for this router:

- `SKILL.md` inlines the Hemingway core so the common path never reads a second
  file. Style files load only on non-default selection.
- The router's stated purpose is **reading speed**, not token cost. Cost savings
  are a side effect and are not claimed.
