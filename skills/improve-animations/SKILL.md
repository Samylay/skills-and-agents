---
name: improve-animations
description: Audit one project's animations/motion with a top-tier model (read-only) and append scoped, verifiable motion tasks to that project's ROADMAP.md for a cheaper model to execute later. Use when you say "/improve-animations <project>", "audit animations for <project>", "queue motion tasks for <project>", or want the plan-expensive/execute-cheap split applied to a repo's UI feel. Motion-specific twin of /improve. Differentiator: it audits ONLY animation/interaction craft against the motion doctrine and never fixes anything itself — for hands-on motion work use a normal session with interaction-craft loaded.
---

# Improve-animations — pricey model audits motion, cheap model executes

Adapted from Emil Kowalski's `improve-animations` skill (github.com/emilkowalski/skills), itself a motion-specific twin of shadcn's `/improve`. Instead of writing plans to a `plans/` dir, this lands **execution-ready tasks in the project's `ROADMAP.md`**, exactly like the sibling `/improve` skill — so a cheaper model can execute them later.

**You are the motion auditor, not the fixer. The ONLY file you may modify is the project's `ROADMAP.md`.**

## The doctrine is not in this file — load it

Before auditing, read the motion doctrine (do not re-derive it):
- The **`interaction-craft`** skill — the rules of record: frequency rule, easing/duration tokens, the ten hard rules, standard patterns, library choices.
- If the project (or your setup) has a per-app **animation plan** defining each app's *animation license* — how much motion each surface earns (full / mid / minimal / out-of-scope) — respect it: don't queue delight on a minimal-license or out-of-scope app.

If a project has no plan, infer its license from the frequency rule (constant/keyboard surfaces → instant, no animation is the correct design; don't queue motion there).

## Workflow

1. **Resolve the project.** One project per run. It must have a real UI surface (skip CLI/pipeline-only repos). If it has no `ROADMAP.md`, create one with a short executor-contract header. Check the app's animation license first — an out-of-scope app means stop and say so.

2. **Recon the motion surface (read-only).** Map where motion lives: framework (Next/React/Tailwind), global CSS tokens (`--ease-*`, `--dur-*`), `prefers-reduced-motion` block, keyframes, `transition-*` usages, gesture/drawer libs (Vaul/Motion/Framer), toast setup (Sonner or hand-rolled). Note the repo's *own* conventions and pick an exemplar component that already does it right.

3. **Audit across the eight categories** (Emil's). For each, check the code against `interaction-craft`:
   1. **Purpose & frequency** — does each animation earn its place? Constant/keyboard actions must be instant; daily surfaces subtle 150–250ms; rare surfaces may delight. Flag animation on the wrong frequency tier.
   2. **Easing & duration** — no default `ease`/`linear`; correct token per motion (out for enters, in-out for moves, drawer curve for sheets); nothing over 300ms (500ms page-level).
   3. **Physicality & origin** — press feedback (`active:scale-[0.97]`) on actionable elements; origin-aware popovers/dropdowns (grow from trigger); never enter from `scale(0)`.
   4. **Interruptibility** — CSS transitions over keyframes so mid-flight retargeting is smooth; gesture-driven motion uses springs.
   5. **Performance** — animate ONLY `transform`/`opacity`/`clip-path`/`filter`; flag any width/height/top/left/margin animation and `transition-all`.
   6. **Accessibility** — a `prefers-reduced-motion: reduce` block exists in global CSS and is actually honored.
   7. **Cohesion & tokens** — shared tokens used everywhere (no scattered magic cubic-beziers/durations); consistent enter pattern across surfaces.
   8. **Missed opportunities** — high-value, license-appropriate moments absent (optimistic UI on frequent mutations, Sonner toasts instead of hand-rolled, a single celebration on a rare meaningful event, skeleton on a genuinely slow load).

4. **Vet before writing.** Re-read the source to confirm each finding is real (not a false positive from a grep); drop anything that's a documented by-design tradeoff or below the app's license. Skip pure style. Skip anything unsafe to touch unattended.

5. **Write tasks, not findings.** Append to `## Tasks` (never edit/reorder existing tasks). Each task must survive execution by a fresh model with the `interaction-craft` skill and zero memory of this audit:
   - `- [ ] **Txx — <imperative motion title>** (S|M) — <what and where, exact file paths + current-code excerpt>. Target: <exact values — cubic-bezier, duration token, spring config>. <why, one line, tied to a doctrine rule>. Verify: <exact command(s) and expected result>.`
   - **Inline the target values** — the executor gets the numbers, not "make it feel better." Reference the token names from `interaction-craft` (e.g. `--ease-out-custom`, `--dur-base`).
   - Sized S (<50 changed lines) or M (a few hundred at most); split anything bigger. Independent: no task may depend on another unchecked one.
   - **Verify for motion is hard — be honest.** Prefer a mechanical predicate: a grep proving the offending property/curve is gone (`! grep -rn 'transition-all' src/`), a test, a build/typecheck passing, or `prefers-reduced-motion` present. If the only real check is "a human looks at it," title it `NEEDS-HUMAN — …` (the executor skips these) rather than faking a Verify.
   - New dependency (Sonner, Vaul, Motion/Framer), a secret, or a judgment call → `NEEDS-HUMAN — …`. Don't add deps unattended, and Emil's own libs are deps.

6. **Update `## Context for the executor`** only if the audit found it stale — and add one line telling the executor to load the `interaction-craft` skill before touching motion.

7. **Validate before finishing.**
   - `git -C <project> status --porcelain` shows `ROADMAP.md` as the only change *you* made — commit only ROADMAP.md (`git add ROADMAP.md`, never `git add -A`); leave concurrent in-flight files untouched.
   - Every new `- [ ]` has inlined target values and a runnable Verify (dry-run the read-only greps now).
   - Cap ~5 new tasks per audit — a big dump goes stale.
   - Commit (`improve-animations: audit <project>, queue N motion tasks`) and push.

## Failure modes

- **App is out of scope / minimal license:** say so, queue nothing (or at most one foundation task like adding the reduced-motion block).
- **Audit finds nothing worth queueing:** say so; don't invent filler motion.
- **Repo already has ≥5 open unchecked tasks:** don't pile on — report findings and let the queue drain.
- **Finding needs a new dep or only a human can verify it:** `NEEDS-HUMAN` task with the evidence, never a normal task.

## Output

End with a one-screen summary: project, its animation license, N motion tasks queued (titles + sizes), NEEDS-HUMAN items, and anything found-but-not-queued with the reason.
