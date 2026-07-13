---
name: distribute-skill-to-all-agents
description: Distribute a skill across all agent skill folders (Claude Code, Codex, Copilot, Cursor, Hermes) so every agent sees it. Use when the user says "distribute this skill", "sync skills across agents", or after creating/updating a skill that should be global. Covers the canonical `.agents` store + the symlink/real-copy layout on this machine.
---

# Distribute a Skill Across All Agents

The user has multiple agent skill locations on this Linux box. A skill must exist in each (or via symlink) to be discoverable by every agent.

## The Locations (this machine)

| Agent | Skills Folder | How it's linked |
|---|---|---|
| Canonical store | `~/.agents/skills/` | **Author skills here first** — single source of truth |
| Claude Code | `~/.claude/skills/` | **Per-skill symlinks → `../../.agents/skills/<name>`** |
| Codex | `~/.codex/skills/` | Independent real copy |
| Copilot | `~/.copilot/skills/` | Independent real copy |
| Cursor | `~/.cursor/skills/` | Independent real copy |
| Hermes | `~/.hermes/skills/` | Independent real copy (snapshots at session start) |

There is **no Pi and no Gemini skills folder** on this machine — don't create them.

## Workflow

1. **Author the skill in `~/.agents/skills/<skill-name>/SKILL.md`** (canonical). Follow the `effective-agent-skills` guidance.
2. **Symlink it into Claude Code** (matches the existing per-skill convention):
   ```bash
   SKILL=<skill-name>
   ln -sfn "../../.agents/skills/$SKILL" "$HOME/.claude/skills/$SKILL"
   ```
3. **Copy it into the real-copy agents that exist on this machine** (skip absent ones — a given machine may only have a subset installed; do NOT create agent dirs):
   ```bash
   for d in codex copilot cursor hermes; do
     [ -d "$HOME/.$d/skills" ] && rsync -a --delete "$HOME/.agents/skills/$SKILL/" "$HOME/.$d/skills/$SKILL/"
   done
   ```
4. **Verify all locations that exist** resolve to a real `SKILL.md`:
   ```bash
   for p in ~/.agents ~/.claude ~/.codex ~/.copilot ~/.cursor ~/.hermes; do
     [ -d "$p/skills" ] || continue
     f="$p/skills/$SKILL/SKILL.md"; [ -f "$f" ] && echo "ok  $f" || echo "MISSING $f"
   done
   ```

## Updating an Existing Distributed Skill

Same flow — re-run step 3 (the `.claude` symlink auto-tracks canonical). Use `rsync -a --delete` (not `cp -r`) so nested files removed from canonical are removed downstream too.

## Pitfalls

- **`~/.claude/skills/<name>` is a symlink, not a folder.** Don't `cp -r` into it — edit the canonical `.agents` copy and the symlink reflects it.
- **Codex / Copilot / Cursor / Hermes are independent copies.** They do NOT auto-update — re-run the rsync loop after every canonical edit.
- **Hermes snapshots skills at session start.** A newly-distributed skill won't appear inside a running Hermes session until restart (fine for future sessions and the other agents immediately).
- **Project-local skills win** — `./.claude/skills/`, `./.codex/skills/`, etc. inside a repo override the global one on collision. This skill only handles GLOBAL distribution.
- **Filename casing matters.** `SKILL.md` must be uppercase.

## When NOT to Use This Skill

- Skill is project-specific → put it in the repo's `./.claude/skills/` etc., not globally.
- Editing one agent's skill only → patch that file directly, don't propagate.
- Removing a skill globally → `rm -rf` from `~/.agents/skills/`, the `~/.claude/skills/<name>` symlink, and each real copy under `~/.codex ~/.copilot ~/.cursor ~/.hermes` (confirm with the user first; deletion is destructive).
