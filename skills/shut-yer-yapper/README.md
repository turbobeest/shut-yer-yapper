# shut-yer-yapper — skill internals

The pitch, install instructions, and before/after examples live in the
[repo README](../../README.md). This file is the map of what is in this
directory, and the rules for changing it.

## Files

```
skills/shut-yer-yapper/
├── SKILL.md                              # Router logic, the invariant, decision process.
│                                         # Inlines the Hemingway core so the default
│                                         # path never reads a second file. ~1.7k tokens.
├── styles/
│   ├── hemingway.md                      # Default. The only style with no rule
│   │                                     # capable of deleting a fact.
│   ├── adhd.md                           # Structure and action-first ordering.
│   ├── ste.md                            # Disambiguation for procedures and handoffs.
│   └── caveman.md                        # Compression. Behind an eligibility gate.
├── references/
│   ├── decision-matrix.md                # Expanded routing table, 6 worked examples,
│   │                                     # blend conflict resolution.
│   └── research-notes.md                 # Sources at pinned SHAs, extracted rule sets,
│                                         # 8 conflicts and their resolutions.
└── evals/
    ├── RUN-EVALS.md                      # Self-contained runbook. Start here to test.
    ├── reasoning-preservation.md         # 10 graded cases for depth survival.
    └── results/TEMPLATE.md               # Scoring sheet.
```

The `/style` command lives at [`commands/style.md`](../../commands/style.md) in
the repo root, because it installs to a different directory than the skill.

## Reasoning Sanctity Invariant

The one thing to understand before editing anything here:

| Layer | Content | Styled? |
|---|---|---|
| 0. Cognition | Thinking, planning, tool choice, exploration | **Never** |
| 1. Substance | Which facts, trade-offs, causes, risks belong in the answer | **Never** |
| 2. Surface | Word choice, sentence length, ordering, formatting | **Yes** |
| 3. Verbatim | Code, diffs, paths, commands, errors, numbers | **Never** |

Any rule you add must operate on Layer 2 only. If a proposed rule could delete a
trade-off, a precondition, a risk, or a real hedge, it does not belong here.

## Editing

Load order at runtime is `SKILL.md` first, then at most one `styles/*.md`. Keep
`SKILL.md` lean — everything added to it is paid for on every invocation.

After editing `SKILL.md`, `styles/ste.md`, or `styles/caveman.md`, re-run **E4**
and **E6** from `evals/RUN-EVALS.md`. Those two cover the failure modes specific
to this router:

- **E4** — does a genuine hedge survive an STE route, or does the modal ladder
  turn "might" into "can"?
- **E6** — does a destructive-operation warning survive an explicitly requested
  Caveman?

Then sync your installed copy from the repo root:

```bash
rsync -a --delete skills/shut-yer-yapper/ ~/.claude/skills/shut-yer-yapper/
```

Restart Claude Code for skill changes to take effect.
