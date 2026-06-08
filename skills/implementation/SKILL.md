---
name: implementation
description: This skill should be used when state.yaml has phase set to implement, when plan.md has gate PASS and code needs to be written, or when the user asks to "implement the plan", "run the implementer", "execute the approved plan". Implementer role: executes plan.md step by step, runs the harness after each change. Does not deviate from the plan.
---

# Skill: implementation

Role: **Implementer** — executes `plan.md` exactly. Does not improvise or add scope.

## Inputs

- `plan.md` with `gate: PASS`
- `state.yaml` with `phase: implement`

## Steps

### 1. Confirm gate

Read `plan.md`. Verify the first line is `**gate**: PASS`. If not, stop and escalate to the Orchestrator.

### 2. Execute each implementation step

For each numbered step in `plan.md`:

**a.** Implement the minimum change required for this step only.

**b.** Run the harness:
```powershell
# Windows
.\harness\harness.ps1

# Linux/macOS
bash harness/harness.sh
```

**c.** If harness **fails**:
- Read the error output carefully
- Revert only the last change
- Fix the specific cause
- Re-run harness
- After 3 consecutive fails on the same step: document under `blockers:` in `state.yaml` and escalate

**d.** If harness **passes**: continue to the next step.

**e.** If the PostToolUse hook is active, harness feedback arrives automatically after each Write/Edit — use that signal.

### 3. Verify acceptance criteria

After all steps: verify each acceptance criterion from `plan.md`. Document the result inline.

### 4. Update state

In `state.yaml`:
- `phase: review`
- `roles_done:` append `implementer`
- `harness.last_run`: ISO 8601 timestamp
- `harness.result: pass`

## Hard constraints

- Do not add functionality outside the scope of `plan.md`. Not even "while I'm in here."
- Do not modify tests to make them pass. A test that fails because of a real bug is a blocker, not a nuisance.
- Keep each git commit to one logical step of the plan.
- If a step is ambiguous, implement the minimal interpretation and note it in `state.yaml` under `notes`.

## Output

Complete diff implementing all plan steps, with harness green.
