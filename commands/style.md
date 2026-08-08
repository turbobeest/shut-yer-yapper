---
description: Set the output style for this session (auto, hemingway, adhd, ste, caveman, blends, reason, off)
argument-hint: "[auto|hemingway|adhd|ste|caveman|adhd+hemingway|hemingway+ste|ste+adhd|reason|off]"
---

Set the session output style using the `shut-yer-yapper` skill.

Requested style: **$ARGUMENTS**

Rules:

1. If `$ARGUMENTS` is empty, report the currently active style in one line, list
   the available options in one line, and stop.
2. If `$ARGUMENTS` is `auto`, enable reasoning-aware routing per the decision
   process in the skill. This is the default.
3. If `$ARGUMENTS` is `reason` or the user wrote "preserve reasoning", lock the
   session to Hemingway or ADHD+Hemingway. Compression styles are disabled for
   the rest of the session.
4. If `$ARGUMENTS` names a pure style or a documented blend, activate it and keep
   it active for the session. Read the matching file under `styles/` if the
   inline summary in `SKILL.md` is not sufficient.
5. If `$ARGUMENTS` is `off`, `normal`, or `normal mode`, deactivate the router and
   return to the default assistant voice.
6. Reject an undocumented blend. Name the closest documented one. Never combine
   full Caveman with STE — they contradict at sentence level.

Confirm the change in a single line. Do not explain the style. Do not restate its
rules. Then continue with whatever the user was doing.

The Reasoning Sanctity Invariant and the Safety Override apply regardless of the
selected style, including when a compression style was explicitly requested.
