---
name: delegating-to-agents
description: How to delegate work to another AI agent (Codex, Claude Code, Copilot, Cursor, Hermes) — picking the right agent, sending prompts to TUI agents, polling progress. Read BEFORE any `tmux send-keys` to an agent, or whenever delegating, relaying, spawning, or orchestrating agent-to-agent work.
---

# Delegating to Agents

## Which agent to pick

- **Coding (default) → Codex CLI.** Strongest coding agent, especially for complex, long-running SWE tasks.
- **Deepest Claude integration → Claude Code.** Use when the task benefits from Claude's `.claude/` conventions, skills, and hooks.
- **Persistent / autonomous / scheduled work → Hermes.** Cross-session memory + built-in scheduler; good for anything that must survive a session or run unattended on a server.
- **Heavy multi-step work:** you as orchestrator + Codex CLI executing in a right-hand `tmux` pane is a solid default setup.

## Sending prompts to a TUI agent (tmux)

1. **ONE single line — never newlines in the message body.** In a TUI, newline = Enter: a multi-line prompt submits at the first line and the rest arrives as fragmented mid-turn steering messages. Use ". " or "; " instead of line breaks, then one explicit Enter. For long instructions, write them to a file and send: `read /tmp/task.md and follow it`.
2. **Send the text and the Enter as two separate `send-keys` calls.** Sending text, then a distinct Enter, avoids the prompt submitting mid-string:
   ```bash
   tmux send-keys -t <target> "your prompt here" ; tmux send-keys -t <target> Enter
   ```
   `<target>` is `session:window.pane` (e.g. `work:0.1`) or a pane id like `%3` (`tmux list-panes -a -F '#{pane_id} #{pane_title}'`).
3. **Quote the prompt in plain double quotes — NEVER escaped `\"`.** Inside the prompt, avoid apostrophes and literal double quotes (write "dont", "wont", "lets"); rephrase instead of escaping. A send that dies with `unexpected EOF` was caused by an escaped `\"`, not the quote type.

## Polling

Keep sleeps SHORT: start at 3-5s, re-check, repeat. Don't `sleep 30`. Read pane output non-destructively with `tmux capture-pane -t <target> -p | tail -20`. After every check, send the user a one-line status: what the agent is doing and whether it's on track.

Claude Code note: after it finishes, it may prefill a predicted next user message — that draft is Claude, not the user.

## Remote VPS / homelab

SSH in first and launch the agent ON the box (e.g. `codex --yolo`) inside a `tmux` session there, then drive that on-box agent. Don't run an agent locally and have it SSH for every step. See the `vps-server-management` skill.

## The agents (background reference)

All use the portable SKILL.md standard; project skills win over global.

- **Codex CLI** (OpenAI, Rust): fastest startup; kernel-level sandboxing; `codex exec` for CI; reads AGENTS.md. Skills: `~/.codex/skills/`.
- **Claude Code** (Anthropic, TS): deepest Claude integration, `.claude/` conventions, live skill hot-reload. Skills: `~/.claude/skills/`.
- **Copilot CLI**: skills at `~/.copilot/skills/`.
- **Cursor**: skills at `~/.cursor/skills/`.
- **Hermes** (Nous Research, Python): persistent autonomous agent — cross-session memory, built-in scheduler, 40+ tools; can orchestrate the other CLIs as workers. Skills: `~/.hermes/skills/`.

Canonical skill store is `~/.agents/skills/`; see `distribute-skill-to-all-agents`.

## Driving interactive CLIs

- Codex, OpenCode: need a real PTY (that's what the tmux pane gives you).
- Claude Code: prefer `claude --print --permission-mode bypassPermissions` (no PTY) for one-shot delegated tasks.
