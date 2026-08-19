# skills-and-agents

A shareable collection of **agent skills** and a **universal agent config**, pulled from a working daily setup and sanitized for public use. Built around Claude Code, but most of it ports to any agent that supports skills (Codex, Copilot, Cursor, and others).

## What's in here

```
global/         Example global config for an agent
  CLAUDE.md       Universal directives — git etiquette, how to work, output style
  specifics.md    Template for machine-/project-scoped rules
  not-qualified.md  Template for the "didn't earn a rule" bucket when mining transcripts
skills/         ~60 self-contained skills (one folder each, SKILL.md inside)
```

## Skills worth starting with

- **`effective-agent-skills` / `write-a-skill`** — how to author skills well (progressive disclosure, triggers, anti-patterns).
- **`interaction-craft`** — Emil Kowalski's web-animation doctrine as a house style for UI work.
- **`improve` / `improve-animations`** — the plan-expensive / execute-cheap pattern: a top model audits and queues verifiable tasks; a cheaper model executes them later.
- **`session-review`** — end-of-session retrospective that turns friction and wins into concrete improvements.
- **`delegating-to-agents`, `distribute-skill-to-all-agents`** — multi-agent orchestration and keeping skills in sync across agents.

Core dev-loop skills (code review, TDD, diagnosing bugs, merge conflicts, domain modeling, guardrail hooks, etc.) aren't vendored here anymore — install [mattpocock/skills](https://github.com/mattpocock/skills) directly, which now covers them.

## Using a skill

Each skill is a folder with a `SKILL.md` (name + description frontmatter, then the instructions). Drop the folder into your agent's skills directory:

- **Claude Code:** `~/.claude/skills/<name>/` (global) or `./.claude/skills/<name>/` (per-project)
- Other agents: their equivalent skills folder.

The `distribute-skill-to-all-agents` skill documents the multi-agent layout.

## A note on the config

`global/CLAUDE.md` is a genuine example you can adapt — swap the `## Context` block for your own profile. `specifics.md` and `not-qualified.md` are **templates**, not real config: keep personal identifiers (names, emails, private hostnames, client names, credentials) in a local, untracked copy, never in a shared repo.

## License

Take what's useful. Attribution appreciated but not required.
