# imat-agentic

Claude Code plugin: agentic development workflow for iMat.

Takes a GitHub Issue from intake to merged PR through 9 phases, driven by specialized agent roles with artifact-based handoffs.

## Skills

| Skill | Invoke | Description |
|---|---|---|
| `agentic-loop` | `/imat-agentic:agentic-loop` | Full 9-phase loop: Issue → PR |
| `flow-router` | `/imat-agentic:flow-router` | Classify task → phase profile |
| `recall` | `/imat-agentic:recall` | Search `.claude/knowledge/` before exploring |
| `codebase-exploration` | Phase skill | Explorer: read-only context gathering |
| `design-gate` | Phase skill | Architect: plan.md + PASS/DISCUSS/RETHINK |
| `implementation` | Phase skill | Implementer: execute plan, run harness |
| `code-review` | Phase skill | Reviewer: confidence-filtered findings |
| `knowledge-capture` | Phase skill | Knowledge: ADRs + patterns |
| `configure` | `/imat-agentic:configure` | Interactive setup wizard |

## Agents

`orchestrator`, `explorer`, `architect`, `implementer`, `reviewer`, `knowledge`, `flow-router`

## Hooks

- **PostToolUse**: runs harness on every Write/Edit during implementation phase
- **PreCompact**: writes `compact-checkpoint.yaml` before context overflow

## Runtime artifacts (project-local)

All runtime artifacts live in the project repo, not in this plugin:

```
project/.claude/
├── agentic/<branch>/      per-task artifacts (state.yaml, exploration.md, plan.md, review.md)
├── knowledge/
│   ├── solutions/         bug-fix captures (/imat-agentic:recall)
│   ├── adrs/              architectural decisions
│   └── patterns/          reusable patterns
└── imat-agentic.local.md  project config (not committed)
```

## Installation

```json
// project/.claude/settings.json
{
  "pluginDirs": ["../imat-agentic"]
}
```

## References

- `references/rules/` — agentic principles, role definitions, code conventions
- `references/templates/` — state.yaml, exploration.md, plan.md, review.md, adr.md
