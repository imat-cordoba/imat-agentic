---
name: codebase-exploration
description: This skill should be used when the agentic-loop is in phase explore, when state.yaml has phase set to explore, or when the user asks to "explore the codebase for this task", "gather context for the issue". Explorer role: read-only context gathering that produces exploration.md. Do not modify any files.
---

# Skill: codebase-exploration

Role: **Explorer** — read-only. Produces `exploration.md` for the Architect.

## Inputs

- `state.yaml` with `phase: explore` at `.claude/agentic/<branch>/state.yaml`
- Recall results (if available at top of `exploration.md`)

## Steps

### 1. Read the current state

Read `.claude/agentic/<branch>/state.yaml` to understand the task objective and any prior recall results.

### 2. Repo structure

- List directory structure to 2 levels: `Get-ChildItem -Depth 2` or `ls -R --max-depth=2`
- Read `AGENTS.md`, `README.md`, and `CLAUDE.md` (if present)
- Read any ADRs in `docs/adr/` that are related to the task domain

### 3. Targeted search

Use grep/search to find code and documentation directly relevant to the issue:

- Search for relevant domain terms in `apps/backend/src/` and `apps/web/`
- Search for related migrations in `apps/backend/src/main/resources/db/`
- Search for related tests

### 4. Produce `exploration.md`

Write `.claude/agentic/<branch>/exploration.md` from `references/templates/exploration.md`.

Required sections:
- **Prior Knowledge** — paste recall results here if available (do not repeat the search)
- **Context** — what the repo does today relative to the issue; if greenfield, state so
- **Relevant files** — key files/dirs, one-line description each
- **ADRs/decisions** — related prior decisions
- **Constraints** — verified constraints (stack versions, conventions, deps)
- **Open questions** — questions the Architect needs answered to design

### 5. Update state

In `state.yaml`:
- `phase: design`
- `roles_done:` append `explorer`
- `artifacts.exploration: exploration.md`

## Constraints

- Do not modify any file other than `state.yaml` and `exploration.md`.
- Do not make assumptions about the solution — only describe what exists.
- Keep `exploration.md` focused: omit information that doesn't affect the design decision.
