---
name: push-skill-to-github
description: Commit and push agent-skill changes to the user's private skills GitHub repo (rooted at ~/.agents). Use after creating or updating any skill, when the user says "push the skill", "push skills to github", "save the skill to my repo", or "update the skills repo". Handles staging, committing, and pushing.
---

# Push Skills to GitHub

For committing any skill change to the user's private skills repo, git root **`~/.agents`** (this is also the canonical skill folder; `~/.claude/skills/<name>` symlinks to `~/.agents/skills/<name>`, and Codex/Copilot/Cursor/Hermes hold independent copies, so re-sync them via `distribute-skill-to-all-agents` before pushing if the skill is global).

Use this after creating or editing a skill. If the skill is distributed to all agents, do that first (`distribute-skill-to-all-agents`), then run this to push the canonical copy.

## Steps

Run these directly in any terminal (no tmux/pane choreography needed):

1. **Confirm the repo and what changed:**
   ```bash
   cd ~/.agents && git status --short
   ```
2. **Stage, commit, push:**
   ```bash
   cd ~/.agents && git add -A && git commit -m "<concise message>" && git push
   ```
3. **Verify** the output shows the push landed (e.g. `main -> main`). If push is rejected, `git pull --rebase` then push again.

## Notes
- Always run git from `~/.agents` (the repo root), not `~/.agents/skills`.
- Write a concise, specific commit message describing the skill change.
- **Never add Claude as a co-author or any self-attribution** in the commit message (matches the user's global git rule).
- Only push to GitHub when the user asks. Don't push speculatively.
- If `~/.agents` isn't a git repo yet, ask the user for the remote URL before initializing.
