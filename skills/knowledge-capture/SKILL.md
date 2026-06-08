---
name: knowledge-capture
description: This skill should be used when the agentic-loop has completed (after PR is open), when the user asks to "capture what we learned", "record this as an ADR", "save this pattern", or when state.yaml is in phase done and new learning emerged. Knowledge role: writes ADRs and patterns to .claude/knowledge/. Does not touch production code or task artifacts.
---

# Skill: knowledge-capture

Role: **Knowledge** — writes to `.claude/knowledge/` and `docs/adr/`. Does not touch implementation code or task artifacts.

## Inputs

- Full run artifacts: `state.yaml`, `exploration.md`, `plan.md`, `review.md`
- Any `clarifications.md` from the run

## Step 1: Filter — is there new learning?

Only proceed if at least one of these is true:

- A non-obvious architectural decision was made (multiple options were weighed)
- A bug's root cause reveals a systemic pattern worth capturing
- A new constraint was discovered (env, stack, legal, external service)
- A reusable implementation pattern emerged that doesn't exist in `.claude/knowledge/patterns/`

If none applies: mark `roles_done: knowledge` in `state.yaml` with a note "no new learning — skipped" and stop.

## Step 2: Choose capture type

| Type | When | Location |
|---|---|---|
| **ADR** | Architectural decision: options were weighed, one was chosen, future code must follow it | `docs/adr/<NNNN>-slug.md` AND `.claude/knowledge/adrs/` |
| **Pattern** | Reusable implementation technique or anti-pattern | `.claude/knowledge/patterns/<slug>.md` |
| **Solution** | Bug root cause + fix, to prevent recurrence | `.claude/knowledge/solutions/<category>/<slug>.md` |

## Step 3: Write the artifact

### ADR (use `references/templates/adr.md`)

Number sequentially: count files in `docs/adr/` and add 1.

Create `docs/adr/<NNNN>-titulo-en-kebab-case.md`.

Copy a summary to `.claude/knowledge/adrs/` with the same filename for searchability.

Required sections: Status, Context, Decision, Consequences.

### Pattern

Create `.claude/knowledge/patterns/<slug>.md`:

```markdown
# Pattern: <name>

## When to use
<!-- Trigger condition -->

## Implementation
<!-- Code example -->

## Anti-pattern
<!-- What NOT to do, and why -->
```

### Solution

Create `.claude/knowledge/solutions/<category>/<slug>.md`:

Categories: `db`, `api`, `frontend`, `auth`, `infra`, `testing`, `tooling`, `perf`

```markdown
# Solution: <title>

## Symptom
<!-- What the bug looked like -->

## Root cause
<!-- Why it happened -->

## Fix
<!-- How it was fixed, with code if applicable -->

## Prevention
<!-- How to avoid this in the future -->
```

## Step 4: Update state

In `state.yaml`:
- `roles_done:` append `knowledge`
- `decisions:` add reference to ADR if created (e.g. `docs/adr/0003-...md`)

## Step 5: Update memory

After writing to disk, create or update a Claude Code memory with:
- Title: one-line summary of the decision/pattern
- Content: decision + justification + reference to the ADR/pattern file
- Make it retrievable for the next task in this codebase
