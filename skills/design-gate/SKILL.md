---
name: design-gate
description: This skill should be used when state.yaml has phase set to design, when exploration.md is ready and the Architect needs to produce a plan, or when the user asks to "design the solution", "produce the plan", "run the design gate". Architect role: produces plan.md with a PASS/DISCUSS/RETHINK verdict. Does not implement code.
---

# Skill: design-gate

Role: **Architect** — produces `plan.md` and emits the Design Gate verdict. Does not write implementation code.

## Inputs

- `exploration.md` (complete, from the Explorer)
- `state.yaml` with `phase: design`
- Any clarification answers from `clarifications.md`

## Steps

### 1. Read exploration and clarifications

Read `.claude/agentic/<branch>/exploration.md` fully. Read `clarifications.md` if present.

### 2. Produce `plan.md`

Write `.claude/agentic/<branch>/plan.md` from `references/templates/plan.md`.

The plan must be **brief and executable**:

- **Objetivo**: one sentence stating what is achieved when this task is done
- **Opciones consideradas**: at least 2 options with explicit pros/cons (even for obvious choices — this is the audit trail)
- **Decisión**: selected option with justification
- **Pasos de implementación**: numbered, unambiguous steps the Implementer can follow without additional context
- **Criterios de aceptación**: verifiable acceptance criteria (harness, manual check, specific behavior)
- **Riesgos**: identified risks + mitigation

### 3. Emit the Design Gate verdict

Evaluate the plan against:
- Is it coherent with repo constraints, stack, and existing ADRs?
- Can each step be executed without additional information?
- Are acceptance criteria verifiable with the harness or a concrete check?
- Does the scope stay within what the issue asks?

**Verdicts**:
- `PASS` — plan is solid, proceed to implementation
- `DISCUSS` — reasonable doubts or trade-offs the user must resolve before proceeding; list specific questions
- `RETHINK` — fundamental problems (wrong scope, contradiction with an ADR, unmitigated high risk); explain and return to exploration with added constraints

### 4. Update state

In `state.yaml`:
- `phase: implement` (if PASS)
- `phase: explore` (if RETHINK), increment `design.rethink_count`
- `phase: design` + `status: waiting` (if DISCUSS)
- `roles_done:` append `architect`
- `artifacts.plan: plan.md`

## Gate declaration format

The first line of `plan.md` must be:
```
**gate**: PASS
```
(or DISCUSS / RETHINK)

## Reviewing cheaply

A 200-line `plan.md` reviewed now is orders of magnitude cheaper than a 2000-line diff reviewed in PR. Invest in the plan — it saves implementation cost.
