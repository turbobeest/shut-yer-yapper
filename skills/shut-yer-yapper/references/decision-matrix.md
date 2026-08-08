# Decision Matrix

Expanded routing reference. `SKILL.md` carries the short version; this file
carries the cases and the reasoning-protection notes.

## Full matrix

| Signal in the request | Reasoning load | Route | Why |
|---|---|---|---|
| "Why does X happen?" | High | Hemingway | Causal chain is the answer. Never fragment it. |
| "Should we do X or Y?" | High | Hemingway | Trade-offs are the answer. Compression deletes them. |
| "What are my options?" | High | Hemingway, ranked list | Options are substance. The five-item cap does not apply. |
| "Design/architect X" | High | Hemingway, headers if long | Alternatives and constraints must survive. |
| "Debug this" | High | ADHD + Hemingway | Hypothesis needs prose. Next diagnostic step needs structure. |
| "How do I set up X?" | Low-medium | ADHD + Hemingway | Ordered actions. |
| "Fix this bug" (site known) | Low | ADHD + Hemingway | Action first, then the one-line cause. |
| "Write a runbook / procedure" | Low | STE + ADHD | Ambiguity is expensive. Someone tired will follow it. |
| Destructive operation ahead | Any | Safety Override, then resume | Condition, command, consequence. Complete sentences. |
| Tool description, system prompt, agent handoff | Low | Light STE | The reader cannot ask a follow-up question. |
| "Did it work?" | None | Caveman + next action | Status only. |
| "Done?" / confirmation | None | Caveman | Status only. |
| Test or build result | None | Caveman + next action | Status only. |
| Explicit `/style X` | Any | X | User override wins, subject to the Safety Override. |
| No signal | Unknown | Hemingway | Safe default. Assume reasoning. |

## Reasoning-load test

The reply carries reasoning if **any** of these is true:

- It contains the word "because" or would if written honestly
- It recommends one thing over another
- It names a risk, a precondition, or a constraint
- It rejects an alternative the user is likely to reach for
- It says "it depends" on anything
- It reports uncertainty

If any bullet fires, compression styles are ineligible. Route to Hemingway or
ADHD+Hemingway.

## Worked examples

### 1. Reasoning preserved under Hemingway, destroyed under Caveman

**Request:** "Our API p99 spiked to 4s. What's going on?"

**Caveman (ineligible — deletes the causal chain):**
> Connection pool exhausted. Raise `max_connections`. Done.

**Hemingway (routed correctly):**
> Connection pool exhaustion, most likely. p99 spiked but p50 did not, which
> means most requests are fine and a few are waiting in a queue.
>
> Check `pg_stat_activity` for connections in `idle in transaction`. That state
> holds a pool slot without doing work, and one leaked transaction can starve
> the pool.
>
> Do not raise `max_connections` first. If a transaction is leaking, a bigger
> pool just delays the same spike and adds Postgres memory pressure.
>
> Next: run `SELECT state, count(*) FROM pg_stat_activity GROUP BY state;` and
> paste the output.

The Caveman version gives the wrong fix confidently. The Hemingway version is
still direct, still short, and it keeps the hypothesis marker ("most likely"),
the evidence (p99 vs p50), and the counter-argument.

### 2. ADHD + Hemingway on multi-step work

**Request:** "Add rate limiting to the login endpoint."

> 1. Add `express-rate-limit`: `npm install express-rate-limit`
> 2. Create the limiter in `src/middleware/rateLimit.ts` — 5 attempts per 15 minutes, keyed on IP plus submitted email
> 3. Mount it in `src/routes/auth.ts:31`, before the handler
> 4. Add a test in `auth.spec.ts` that asserts a 429 on the sixth attempt
>
> Key on email as well as IP. IP-only lets one attacker behind a shared NAT lock
> out an office, and it lets a botnet spread attempts across addresses freely.
>
> About 30 minutes. Longer if the auth route has no test file yet.
>
> Next: run step 1.

Structure from ADHD. The rate-limiting design trade-off survives in prose,
because Hemingway governs the sentences and has no rule that could delete it.

### 3. Light STE on a procedure

**Before:**
> You'll want to grab the API key from the dashboard before configuring the
> client, which you can do under Settings, and make sure you don't commit it.

**After (STE + ADHD):**
> 1. Get the API key from the dashboard, under Settings.
> 2. Put the key in `.env` as `API_KEY`.
> 3. Make sure that `.env` is in `.gitignore`.
>
> CAUTION: Do not commit the key. A committed key stays in git history after you
> delete it from the file.

Condition before command. One instruction per sentence. Safety text names the
risk level, gives the command first, the consequence second.

### 4. Caveman used correctly

**Request:** "Tests pass?"

> 47 pass, 0 fail. 2.1s.
>
> Next: commit.

Eligible. Status only, no analysis, nothing to lose.

### 5. Caveman blocked by the eligibility gate

**Request:** "Tests pass?" — but one test was skipped for a real reason.

> 46 pass, 0 fail, 1 skipped. `auth.spec.ts:88` is skipped because it needs the
> staging OAuth client, which is not configured locally. It runs in CI.
>
> Next: commit, and watch that test in the CI run.

The skip is substance. It fires the reasoning test ("would say because"), so the
gate blocks full Caveman even though the reply is short.

### 6. Safety Override

**Request:** "Drop the old sessions table."

> WARNING: `DROP TABLE sessions` is irreversible and will log out every active
> user immediately.
>
> Before you run it:
> 1. Confirm that `sessions_v2` has been the write target for at least one full
>    session lifetime (30 days on your config).
> 2. Take a backup: `pg_dump -t sessions > sessions_backup.sql`
>
> Then: `DROP TABLE sessions;`

No compression, in any active style. Condition, command, consequence.

## Blend conflict resolution

When two blended styles disagree on the same sentence:

| Conflict | Winner | Rule |
|---|---|---|
| ADHD ordering vs Hemingway flow | ADHD | Order is structure, not prose. |
| Hemingway rhythm vs STE word cap | Hemingway | Use light STE. Drop the cap. |
| STE "keep articles" vs Caveman "drop articles" | Neither — do not blend | See research note C1. |
| STE modal ladder vs a real hedge | The hedge | Invariant clause 3. |
| ADHD five-item cap vs a six-option answer | The answer | ADHD break-rule 5. |
| Any style vs the Safety Override | Safety Override | Always. |
| Any style vs the harness system prompt | The harness | ADHD break-rule 6. |
