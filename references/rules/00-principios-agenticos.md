# Agentic Principles (iMat)

Active in every agent session. All roles follow these.

## Core principles

- **80% understand, 20% apply**: explore repo + ADRs + memories before any change. In greenfield, this step is lightweight but never skipped.
- **Design Gate before implementing**: the Architect emits `PASS / DISCUSS / RETHINK` on `plan.md`. No PASS, no implementation.
- **Deterministic validation first**: the harness runs before the LLM reviewer. A red harness blocks progress.
- **Review by confidence**: only findings with high confidence are surfaced as blockers (Security / Correctness). Others are notes.
- **Memory between runs**: every architectural decision becomes an ADR in `docs/adr/` and a `.claude/knowledge/` entry. Resolved reasoning is not repeated.
- **Auditable artifacts**: each task produces `state.yaml`, `exploration.md`, `plan.md`, `review.md` in `.claude/agentic/<branch>/`.

## Flow conventions

- `state.yaml` is the **single source of truth** for task state.
- Read-only or side-effect-free steps (running harness, reading repo) can execute without explicit confirmation.
- Destructive or external steps (push, PR, installing deps) **always require confirmation**.
- When a step blocks, the role documents the blocker under `blockers:` in `state.yaml` and escalates to the Orchestrator.
- No production code changes without an approved `plan.md`.

## Language

- Code and technical docs: **English**.
- Business docs, UX copy, commits: **Spanish**.
- Commit format: `tipo(scope): descripción breve` (Conventional Commits).
