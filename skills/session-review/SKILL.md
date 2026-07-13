---
name: session-review
description: End-of-session retrospective that turns this session's friction and wins into at most 3 concrete improvements (memory updates, CLAUDE.md/skill/agent proposals, small fixes) with strict anti-bloat rules. Use when the user signals the session is ending — "we're done", "that's it for today", "session over", "wrapping up", "call it a day" — or asks what could be improved about how we worked.
---

# Session Review

Look back over THIS session only and extract what would have made it go
better. The deliverable is a short report + a few applied updates — not an
essay, not a pile of new memories.

## Step 0 — Leave a durable trace (optional, if your setup has one)

If you keep a running log of deliveries or a session journal outside the
model's memory, append a short dated entry for anything real this session
shipped (a deploy, a doc to review, a system built, a post published). This
is the human-readable trace that survives when the conversation is gone. Skip
for anything confidential that shouldn't land in a personal log.

## Step 1 — Scan the session for signal

Walk the conversation and collect only these five kinds of evidence:

1. **Corrections** — anything the user corrected, redirected, or descoped.
2. **Friction** — errors, retries, permission denials, wrong assumptions,
   files changed under you by concurrent sessions, limits hit.
3. **Near-misses** — automation that would have misbehaved unattended
   (e.g. a cron/loop interacting badly with an error mode).
4. **Wins worth repeating** — a pattern that worked and isn't written down.
5. **Loose ends** — anything shipped but not verified end-to-end.

If a category is empty, say nothing about it. "Nothing worth changing" is a
valid, complete outcome — do not manufacture insights.

## Step 2 — Classify into actions (hard cap: 3 proposals total)

- **Apply now (no approval needed):** memory updates. MERGE-FIRST: update an
  existing memory (especially the one accumulating interaction/working-style
  patterns) rather than creating a new file. Create a new memory only for a
  genuinely new topic. Delete/correct any memory this session proved stale.
- **Propose (the user decides):** CLAUDE.md rule changes, new/changed skills,
  agent-definition changes, code fixes they didn't ask for. Present each in
  ≤2 sentences with the session evidence that motivates it.
- **Remind:** loose ends from item 5, one line each.

## Step 3 — Report

Short prose, most useful first: proposals (≤3), memory updates made,
reminders. Then stop — apply proposals only if the user approves.

## Anti-bloat rules (these outrank everything above)

- ≤3 proposals per session; if more candidates exist, keep the ones that
  would have changed this session's outcome and drop the rest silently.
- A CLAUDE.md rule proposal must cite friction that recurred (this session +
  a memory/log showing it before) OR was expensive once. One-off annoyances
  are not rules. Keep the instructions file laws-not-tips and tight — if
  adding a line, propose which line to cut.
- A new-skill proposal needs a trigger that will plausibly fire again.
- Memory: net growth target is ~zero — pair additions with a merge, trim,
  or deletion. Never duplicate what git history or CLAUDE.md already record.
- If an observation needs more than this session as evidence, leave it — one
  session isn't a trend. Don't promote a single data point to a rule.
