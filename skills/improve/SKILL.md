---
name: improve
description: Audit one project with a top-tier model (read-only) and append scoped, verifiable tasks to that project's ROADMAP.md for a cheaper model to execute later. Use when you say "/improve <project>", "audit <project>", "queue improvements for <project>", or want the plan-expensive/execute-cheap split applied to a repo. Differentiator: it produces execution-ready tasks, it never fixes anything itself — for hands-on fixes use a normal session.
---

# Improve — pricey model audits, cheap model executes

Pattern popularized by shadcn's `/improve` command: the most capable model reads the codebase and writes execution plans; a cheaper model runs them later (in a fresh session, a CI job, or an automated task runner). The split is the point — spend your best model where a wrong call is expensive (planning), spend a cheap one where the work is bounded and checkable (execution).

**You are the auditor, not the fixer. The ONLY file you may modify is the project's `ROADMAP.md`.**

## Workflow

1. **Resolve the project.** One project per run. If it has no `ROADMAP.md`, create one with a short executor-contract header and a `## Context for the executor` section (what the executor needs to know to run a task cold).

2. **Read-only audit.** Read the code, run tests, linters, `git log`, greps — anything non-mutating. Hunt in this order:
   - bugs and correctness hazards (incl. error paths that swallow failures)
   - drift between docs/README and reality (check claims against live state)
   - missing tests around code that recently changed or has none
   - performance and cost (wasted API calls, unbounded loops, missing timeouts)
   - tech debt that blocks the above
   Skip pure style. Skip anything unsafe to touch unattended (user data, DBs, migrations, secrets, live service state, new dependencies).

3. **Write tasks, not findings.** Append to `## Tasks` (never edit or reorder existing tasks). Each task must survive execution by a fresh model with zero memory of this audit:
   - `- [ ] **Txx — <imperative title>** (S|M) — <what and where, with file paths>. <why, one line>. Verify: <exact command(s) and expected result>.`
   - Sized S (<50 changed lines) or M (a few hundred at most); split anything bigger.
   - Independent: no task may depend on another unchecked task landing first.
   - The Verify command must be runnable from the repo root and must fail before the fix / pass after. If you can't state one, the task isn't ready — drop it or mark it `NEEDS-HUMAN`.
   - Anything requiring a new dependency, a secret, a service restart, or a judgment call → title it `NEEDS-HUMAN — …` (the executor skips these).

4. **Update `## Context for the executor`** only if the audit found it stale (wrong test counts, moved paths) — correct facts, don't expand scope.

5. **Validate before finishing.**
   - `git -C <project> status --porcelain` shows `ROADMAP.md` as the only change *you made* — leave any concurrent in-flight files untouched and commit only ROADMAP.md (`git add ROADMAP.md`, never `git add -A`).
   - Every new `- [ ]` line has a Verify note; dry-run the read-only verify commands now.
   - Cap ~5 new tasks per audit — a 20-task dump goes stale before it's executed.
   - Commit the ROADMAP.md change (`improve: audit <project>, queue N tasks`) and push.

## Failure modes

- **Audit finds nothing worth queueing:** say so; don't invent filler tasks.
- **Repo already has ≥5 open unchecked tasks:** don't pile on — report the findings and let the queue drain.
- **Finding is real but not safely automatable** (touches data, needs a restart, ambiguous spec): `NEEDS-HUMAN` task with the evidence, never a normal task.

## Output

End with a one-screen summary: project, N tasks queued (titles + sizes), NEEDS-HUMAN items, and anything found-but-not-queued with the reason.
