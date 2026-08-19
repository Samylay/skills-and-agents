# Specifics (template)

Machine- and project-bound rules. These do **not** belong in the universal
`CLAUDE.md`, only load/apply the section relevant to the machine you're on.
This file is a **template**: replace the example sections with your own
machines and projects. Keep each section short and factual.

---

## Example: Work machine

**Stack & domain**
- The languages, frameworks, and CMS you actually use here. Name versions when
  they matter (e.g. "Next.js 16 + Tailwind v4"). Note any domain that dominates
  the work (e.g. accessibility, data pipelines, embedded).

**Workflow**
- Any machine-specific habits the agent should follow, e.g. "after a UI change,
  start the dev server so I can test in the browser; kill stray dev servers when
  done." Encode the *behavior*, not one-off instructions.
- Whether to set up CI / test infra proactively, or stay feature-first unless
  asked. State the default and note per-project exceptions.

**Projects:** list the repos on this machine so the agent can resolve a name to
a project without asking.

---

## Example: Second machine (different OS / role)

**Machine:** OS, window manager, shell, editor, terminal, and package managers.
If it's a distro with native conventions (e.g. Arch + yay/pacman), say "use the
native solution, don't suggest distro-agnostic workarounds."

**Workflows**
- Recurring machine-specific tasks (package audits, compliance checks against a
  spec, sysadmin work) and how you want them handled.

**Collaborators:** if you regularly reconcile code with named teammates, note how
to resolve conflicts (e.g. "prefer theirs over mine when I say so"). *Keep real
names out of a shared/public copy of this file.*

**Projects:** personal vs. school/work, with a one-word stack tag each.

---

## Notes on posture & secrets

- You can encode a *posture*, e.g. "fine with running privileged/sudo commands
  directly; I supply the password when prompted." **Encode the posture, never
  the password**, and never any token, key, or credential value.
- Personal identifiers (full name, email, logins, private hostnames, client
  names, staging URLs) do not belong in a file you may share. Keep those in a
  local, untracked copy.
