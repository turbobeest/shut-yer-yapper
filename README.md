<div align="center">

# shut-yer-yapper

**Stop the yapping. Not the thinking.**

An adaptive clarity router for Claude Code. It picks the clearest output style for
each reply — and refuses every rule that would make your agent dumber to make it
shorter.

</div>

---

## The problem

Opus 5 yaps. The usual fix is a compression skill, and compression skills have a
failure mode nobody talks about:

> **You:** Should we shard this table?
>
> **A compression skill:** `Shard on user_id. Range scans slower. Do it.`

That answer is 60% shorter and it is *worse than useless*. It kept the
recommendation and deleted the reason the recommendation might be wrong. You
cannot evaluate it. You cannot catch the problem it missed.

Here is the same question through this router:

> Shard on `user_id`. It matches your access pattern — 90% of reads filter by user.
>
> Cost: cross-shard range scans on `created_at` go from one index seek to a
> scatter-gather across every shard. Your nightly export does exactly that.
>
> Move the export to a read replica first. Then shard.

Four sentences longer. The difference between a good decision and a bad one.

## The idea

**Style shapes prose. Style never shapes thought.**

Four layers. Style touches exactly one.

| Layer | Content | Styled? |
|---|---|---|
| 0. Cognition | Thinking, planning, tool choice, exploration | **Never** |
| 1. Substance | Which facts, trade-offs, causes, and risks belong in the answer | **Never** |
| 2. Surface | Word choice, sentence length, ordering, formatting | **Yes** |
| 3. Verbatim | Code, diffs, paths, commands, errors, numbers | **Never** |

Every rule in this repo operates on Layer 2. Nothing else is negotiable.

## What it actually does

It reads the four best output-style skills on GitHub, extracts their
non-overlapping rules, and **routes between them per reply** instead of forcing
one permanent voice.

| Style | Good at | Where it hurts you |
|---|---|---|
| [Hemingway](skills/style-router/styles/hemingway.md) | Everything with a "because" in it | — (this is the default) |
| [ADHD](skills/style-router/styles/adhd.md) | Action-first structure, ordered steps | Caps lists at 5 — bad when you have 8 findings |
| [STE](skills/style-router/styles/ste.md) | Unambiguous procedures, agent handoffs | Turns "might" into "can" — fabricates confidence |
| [Caveman](skills/style-router/styles/caveman.md) | Status lines, confirmations | Deletes trade-offs. Gated behind an eligibility check. |

Routing, first match wins:

1. Explicit user preference or sticky session mode
2. **Reasoning load** — architecture, debugging, trade-offs, "why", "should I" → Hemingway
3. Ordered actions → ADHD + Hemingway
4. Ambiguity is expensive — safety, runbooks, agent handoffs → Hemingway + light STE
5. Short status or confirmation → Caveman + one next action
6. No signal → Hemingway

Caveman sits behind a gate: **a reply that would say "because" is never eligible,
however short it is.**

## Install

```bash
git clone https://github.com/turbobeest/shut-yer-yapper.git
cd shut-yer-yapper
cp -r skills/style-router ~/.claude/skills/style-router
mkdir -p ~/.claude/commands && cp commands/style.md ~/.claude/commands/style.md
```

Restart Claude Code. Then:

```
/style auto
```

Full options, project-level install, plugin install, and the always-on
`SessionStart` hook: [INSTALL.md](INSTALL.md).

## Usage

| Command | Effect |
|---|---|
| `/style auto` | Reasoning-aware routing (default) |
| `/style hemingway` \| `adhd` \| `ste` \| `caveman` | Force a pure style |
| `/style adhd+hemingway` \| `hemingway+ste` \| `ste+adhd` | Force a blend |
| `/style reason` | Lock to Hemingway. Compression disabled. |
| `/style off` | Back to the default assistant voice |

Plain English works too: *"be brief"*, *"too verbose"*, *"preserve reasoning"*,
*"normal mode"*.

## Before / after

**Before**

> Great question! There are a few different things that could potentially be going
> on here. It's worth noting that the most common cause of this particular issue
> tends to be a mismatch between the expected and actual token format. You'll want
> to take a look at your middleware configuration to see if that might be the
> culprit. Let me know if you'd like me to dig deeper!

**After** — Hemingway

> Token format mismatch, most likely. Check `src/middleware/auth.ts:31` — the
> verifier expects a raw JWT, and the client is sending `Bearer <jwt>`.
>
> Next: log the header value at line 29 and paste it.

**Multi-step work** — ADHD + Hemingway

> 1. `npm install express-rate-limit`
> 2. Create `src/middleware/rateLimit.ts` — 5 attempts per 15 minutes, keyed on IP plus email
> 3. Mount at `src/routes/auth.ts:31`, before the handler
> 4. Assert a 429 on the sixth attempt in `auth.spec.ts`
>
> Key on email as well as IP. IP-only lets one attacker lock out a shared office,
> and lets a botnet spread attempts across addresses.
>
> Next: run step 1.

**Status** — Caveman is correct here

> 47 pass, 0 fail. 2.1s.
>
> Next: commit.

## The conflicts nobody else resolved

Merging four style skills is not concatenation. They contradict each other.
[Eight documented conflicts](skills/style-router/references/research-notes.md),
three that matter:

**C1 — Hard contradiction.** Caveman drops articles and allows fragments. STE
Rule 4.2 explicitly forbids omitting words: *"Ensure file exists before running"*
must become *"Make sure that the file exists before you run the command."* These
cannot coexist in one sentence. Caveman+STE is not an offered blend.

**C3 — The dangerous one.** STE's modal ladder converts `may / might / could` to
`can`. Applied to an *assessment* rather than an *instruction*, that launders a
hypothesis into a claim — "this might be a race condition" becomes "this can be
a race condition." ADHD's own pre-send check contradicts it directly: *"keep a
hedge that carries real uncertainty; deleting it manufactures confidence."*
Resolution: the ladder applies to instructions, and is **suspended for
assessments**.

**C4 — Silent truncation.** ADHD caps lists at five items. Ask "what are all the
ways this can fail" and get eight, and the cap deletes three. Resolution: the cap
governs *action* lists only, never findings that are themselves the answer.

## Honest cost note

The upstream Caveman repo publishes its own
[HONEST-NUMBERS.md](https://github.com/JuliusBrussee/caveman/blob/main/docs/HONEST-NUMBERS.md),
and it is worth your time: ~65% output reduction on verbose replies, **0% input
reduction**, and ~1–1.5k input tokens *added* per turn. On terse Q&A, compression
skills are net-negative.

So this one optimizes for **reading speed, not token cost.** `SKILL.md` inlines
the default style so the common path never reads a second file. Treat any cost
saving as a side effect, and A/B it against your own billing page before you
believe anyone's numbers, including these.

## Testing it

Ten graded cases for whether reasoning actually survives:
[`skills/style-router/evals/`](skills/style-router/evals/). Scored on five axes —
route, substance, uncertainty, verbatim, brevity. Pass requires all five.

```
Read skills/style-router/evals/RUN-EVALS.md and follow it.
```

The two that matter most are **E4** (does "might" survive an STE route?) and
**E6** (does a destructive-op warning survive an explicitly requested Caveman?).

## Credits

Distilled from, and genuinely indebted to:

- **[JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman)** — compression, intensity levels, verbatim preservation, and the most honest benchmarking doc in this space
- **[ayghri/i-have-adhd](https://github.com/ayghri/i-have-adhd)** — action-first structure, the ten rules, the six break conditions
- **[AminBlg/SimpleEnglish](https://github.com/AminBlg/SimpleEnglish)** — the ASD-STE100 Issue 9 rule catalog, Untouchables, and slop tables
- **[danyuchn/asd-ste100-skill](https://github.com/danyuchn/asd-ste100-skill)** — the agent-as-reader framing, and *"flag the trade-off rather than silently simplify"* — the closest thing in the source material to this repo's invariant

Also evaluated: [TheAngryByrd/simplified-technical-english-skill](https://github.com/TheAngryByrd/simplified-technical-english-skill),
[nuelcyoung/asd-ste100](https://github.com/nuelcyoung/asd-ste100).

Every source was read at a pinned commit;
[SHAs are recorded](skills/style-router/references/research-notes.md).

ASD-STE100 is a registered trademark of ASD. No approved dictionary is reproduced
here — the official standard is a free download at
[asd-ste100.org](https://www.asd-ste100.org/).

## License

MIT. See [LICENSE](LICENSE).
