# STE (Simplified Technical English)

Principles distilled from ASD-STE100 Issue 9 as encoded by
`AminBlg/SimpleEnglish` (commit `59bf670`, primary source) and
`danyuchn/asd-ste100-skill` (commit `8564f89`, agent-reader framing).

No dictionary is reproduced here. ASD's ~900-word approved list is their
copyrighted standard, free at asd-ste100.org. This file carries the mechanics
only: **one word, one meaning, one part of speech.**

STE is a **disambiguation** style. It costs words to buy precision. It is the
opposite of Caveman, not a stronger version of it.

## When to use

- Safety-critical instructions and destructive operations
- Runbooks and procedures someone will follow while tired
- Agent-to-agent handoffs, tool descriptions, system prompts
- Non-native English readers or a translation pipeline

## Two intensities

| Intensity | Apply | Skip |
|---|---|---|
| **Light** (default in blends) | One term per concept. Active voice. Simple tenses. Condition before command. One instruction per sentence. No semicolons. No ellipsis. | Word caps. Modal ladder. Vocabulary rulings. |
| **Full** | All of the above plus 20/25-word caps and vocabulary discipline | — |

Use light STE unless the user names STE or compliance.

## Core rules

**Classify first.** Every other rule depends on this.

| | Procedural | Descriptive |
|---|---|---|
| Verb form | Imperative: "Run the migration." | Simple present or past |
| Word cap (full only) | 20 | 25 |
| Unit | One instruction per sentence | One topic per paragraph, max six sentences |

Do not mix the two in one passage.

**Words**
- One item, one name. Do not write "config" here and "settings" there.
- Do not use a technical noun as a verb, or a technical verb as a noun.
- Keep multi-word nouns to three words or fewer. Break longer chains with a
  preposition: "the connection pool timeout configuration value" becomes
  "the timeout value for the connection pool".

**Verbs**
- Active voice. Passive only when the actor is genuinely unknown.
- Simple tenses only. "We received the report", not "We have received the report".
- Use `-ing` only as a noun ("logging"), never as a verb.
- Put the action in a verb, not a noun phrase: "compress the file", not "perform
  compression of the file".

**Sentences**
- Do not omit words. Keep articles. Keep "that". Do not use contractions.
  This is Rule 4.2, and it is the anti-terseness rule: *"Ensure file exists
  before running"* becomes *"Make sure that the file exists before you run the
  command."* STE is short sentences with complete grammar, never telegraph style.
- Put a required condition before the command, divided by a comma:
  "If the build fails, read the log."
- No semicolons. Write two sentences.
- Use a vertical list for three or more steps or conditions.

**Safety text**
1. Name the risk level: WARNING for injury, CAUTION for damage or data loss.
2. Give the command or condition first.
3. Give the risk or result second.

Never bury the instruction after the explanation.

## The modal ladder — and its hard limit

STE's own ladder:

| You wrote | STE writes |
|---|---|
| should (requirement) | must |
| should (recommendation) | delete, or state as fact |
| may / might / could (possibility) | can |
| would (hypothetical) | restructure: "If X occurs, Y occurs." |

**This ladder is suspended whenever the modal carries epistemic uncertainty.**

STE assumes an author who knows the answer and is removing ambiguity from a
known procedure. That assumption does not hold when the model is reasoning about
an unfamiliar system. Converting "this might be a race condition" into "this can
be a race condition" does not remove ambiguity — it fabricates confidence, and
it violates Enforcement Clause 3 of the Reasoning Sanctity Invariant.

Rule: apply the ladder to **instructions**. Never apply it to **assessments**.

- Instruction: "You should restart the service" → "Restart the service." Correct.
- Assessment: "The leak might be in the connection pool" → leave it. It is a
  hypothesis, and the hedge is the most important word in the sentence.

## Slop substitutions

Safe in every style. These words carry no fact.

| Slop | Write |
|---|---|
| leverage, utilize | use |
| in order to | to |
| prior to | before |
| it is worth noting that / it's important to | (delete — state the fact) |
| simply, just, easily, seamlessly, effortlessly | (delete) |
| robust, powerful, comprehensive, performant | (delete, or give the measurement) |
| enables you to, allows you to | you can |
| is designed to, aims to | (delete — say what it does) |
| facilitate | help |
| delve into, dive into | read, examine |
| due to the fact that | because |
| in the event that | if |
| gracefully handles | (say what it does: "retries three times, then stops") |
| out of the box | by default |
| under the hood | internally |
| plethora, myriad | many |
| e.g. / i.e. / etc. | for example / that is / (name the items) |

## Untouchables

Code blocks, inline code, identifiers, CLI commands, flags, file paths, quoted
error messages, log lines, product names, API endpoints, config keys, numbers
with units. Exact, always, even when they break a vocabulary rule.

## Blend notes

- **Hemingway + light STE** is the recommended clarity blend. STE contributes
  one-term-per-concept and condition-before-command. Hemingway keeps the rhythm.
- **STE + ADHD** is the procedure blend. ADHD numbers the steps and names the
  next action. STE writes each step.
- **Never blend with full Caveman.** Rule 4.2 forbids exactly what Caveman
  requires. See `references/research-notes.md`, conflict C1.

## Limits

STE is for technical facts and instructions. Do not apply it to persuasive or
narrative writing — it deletes voice by design. This is an unofficial aid, not
affiliated with ASD or STEMG. ASD-STE100 is a registered trademark of ASD.
