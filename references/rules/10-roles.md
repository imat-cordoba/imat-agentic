# Agent Roles (iMat)

Six specialized roles that chain via artifacts. A single AI agent can execute multiple roles in sequence within a session.

## Role table

| Role | Responsibility | Input | Output |
|---|---|---|---|
| **Orchestrator** | Read issue, create `state.yaml`, route phases, operate the Design Gate, execute commit/PR | Issue | `state.yaml` updated |
| **Explorer** | Gather context (repo + ADRs + knowledge); lightweight in greenfield | `state.yaml` | `exploration.md` |
| **Architect** | Brief plan with options; emit gate verdict | `exploration.md` | `plan.md` + gate |
| **Implementer** | Execute approved plan; run harness after each change | `plan.md` | diff + green harness |
| **Reviewer** | Confidence-filtered review (Security/Correctness/Performance) | diff | `review.md` |
| **Knowledge** | Capture ADR/pattern if new learning emerged | full run | `docs/adr/*` + `.claude/knowledge/` |

## Handoff chain

```
Issue
  └─ Orchestrator → state.yaml (phase: explore)
       └─ Explorer → exploration.md (phase: design)
            └─ Architect → plan.md + GATE (phase: implement | rethink)
                 └─ Implementer → diff + harness (phase: review)
                      └─ Reviewer → review.md (phase: finalize | rethink)
                           └─ Orchestrator → commit + PR (phase: done)
                                └─ Knowledge → ADR + .claude/knowledge/
```

## Role constraints

- **Explorer**: read-only. Does not modify repo files (only `state.yaml` and `exploration.md`).
- **Architect**: may suggest but does not implement. Only writes `plan.md`.
- **Implementer**: only works with a `plan.md` with gate `PASS`. Runs harness after every change.
- **Reviewer**: does not modify code. Only produces `review.md`.
- **Knowledge**: only writes to `docs/adr/` and `.claude/knowledge/`. Does not touch code or task artifacts.
