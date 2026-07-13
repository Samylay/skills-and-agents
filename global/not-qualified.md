# Not Qualified (template)

When you mine past transcripts to build up `CLAUDE.md` / `specifics.md`, most
candidate "rules" shouldn't become rules. Keep them here so you don't
re-discover and re-debate them, and so watch-list items can graduate later if a
second sighting corroborates them. This is a **template** — the sections below
explain the buckets; fill them with your own findings (and keep anything
personal out of a shared copy).

---

## Watch-list — promote only on a 2nd sighting

A behavior seen **once** is not a rule. Park it here with the context and a note
to promote it only if it recurs on another session/machine. This stops a single
data point from hardening into a law.

## Dropped — noise, one-offs, not behavioral rules

Casual register (nicknames, slang) is tone, not an instruction — match the tone,
don't encode a persona. One-off troubleshooting steps are not standing rules.
Record what you deliberately chose *not* to encode, so it doesn't resurface.

## Looked for and did NOT find (don't fabricate these)

Note the preferences you searched for and genuinely could not find evidence of
(commit-message convention, formatter/linter choices, framework defaults). This
is a guard against the agent inventing a preference the user never expressed.

## Security flags (action items, not rules)

If transcript mining ever surfaces a **leaked credential** (a token, key, or
license in plaintext), record it here as a real-world cleanup task — **rotate
the credential** at its source — and never paste the value itself into this or
any tracked file.
