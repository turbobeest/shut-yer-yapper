# Install

The skill activates on request and then persists for the session. Nothing runs in
the background and nothing phones home — it is markdown.

## User-level (recommended)

Available in every project.

```bash
git clone https://github.com/turbobeest/shut-yer-yapper.git
cd shut-yer-yapper
cp -r skills/style-router ~/.claude/skills/style-router
mkdir -p ~/.claude/commands && cp commands/style.md ~/.claude/commands/style.md
```

Restart Claude Code, then run `/style auto`.

Verify with `/doctor`, or just check the files landed:

```bash
ls ~/.claude/skills/style-router/SKILL.md ~/.claude/commands/style.md
```

## Project-level

Scoped to one repo, and committable so your team shares it.

```bash
mkdir -p .claude/skills .claude/commands
cp -r skills/style-router .claude/skills/style-router
cp commands/style.md .claude/commands/style.md
```

## As a plugin

The layout already matches the marketplace convention. Drop `skills/style-router`
into a plugin's `skills/` and `commands/style.md` into its `commands/`.

## Always-on

By default the skill loads when it is invoked or when the model matches your
request against its description. To have it active from the first turn of every
session, add a `SessionStart` hook to `~/.claude/settings.json`:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup|resume",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Use the style-router skill for all user-facing replies this session. Default to Hemingway. The Reasoning Sanctity Invariant applies: style shapes prose, never thought.'"
          }
        ]
      }
    ]
  }
}
```

That costs one line of context per session. The skill body still loads only when
invoked.

If you already have a `SessionStart` array, append to it rather than replacing it.

## Uninstall

```bash
rm -rf ~/.claude/skills/style-router ~/.claude/commands/style.md
```

Or just say "normal mode" to turn it off for the current session without removing
anything.

## Token cost

| File | Size | Loaded |
|---|---|---|
| `SKILL.md` | ~6.7 KB (~1.7k tokens) | On invocation |
| `styles/*.md` | ~1.0–1.5k tokens each | Only when that style is selected |
| `references/`, `evals/` | — | Only when explicitly read |

`SKILL.md` inlines the Hemingway core, so the default path never reads a second
file. Worst case for a single reply is `SKILL.md` plus one style file.

Read the [honest cost note](README.md#honest-cost-note) before expecting savings.
This optimizes for reading speed, not tokens.

## Compatibility

Written against Claude Code's Agent Skills format (`SKILL.md` with YAML
frontmatter, progressive disclosure via relative file references). The style
files are plain markdown with no Claude-specific syntax, so they port to other
harnesses — but the `/style` command and the skill frontmatter are Claude Code
specific.
