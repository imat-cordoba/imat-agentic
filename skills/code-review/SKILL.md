---
name: code-review
description: This skill should be used when state.yaml has phase set to review, when implementation is done and the diff needs review, or when the user asks to "review the implementation", "run the reviewer", "code review with confidence filter". Reviewer role: read-only review of the diff filtered by confidence threshold. Produces review.md. Does not modify code.
---

# Skill: code-review

Role: **Reviewer** — read-only. Produces `review.md`. Does not modify code or artifacts.

## Inputs

- Complete diff of the implementation (`git diff origin/main...HEAD`)
- `plan.md` (to verify diff stays within scope)
- `state.yaml` with `phase: review`
- Confidence threshold from `.claude/imat-agentic.local.md` (default: `80`)

## Steps

### 1. Read the diff and plan

Read `plan.md` to understand what was intended. Read the full diff. Note any file outside the expected scope.

### 2. Scope check

Verify the diff is within the scope defined in `plan.md`. If out-of-scope changes are present, mark as a finding.

### 3. Review by category

For each finding, assign:
- **Category**: `Security` | `Correctness` | `Performance` | `Maintainability` | `Style`
- **Confidence**: integer 0–100
- **Action**: derived from category + confidence (see filter below)

**Focus areas:**
- **Security**: injection, exposed secrets, missing auth checks, unvalidated external input
- **Correctness**: wrong logic, missing edge cases, race conditions, error paths not handled
- **Performance**: N+1 queries, unbounded loops, resources not closed

**Do not surface:**
- Maintainability or Style findings below the confidence threshold
- Hypothetical future problems ("could be a problem if...")
- Preferences with no correctness impact

### 4. Apply confidence filter

Read `confidence_threshold` from `.claude/imat-agentic.local.md` (default: 80).

| Confidence | Category | Action |
|---|---|---|
| ≥ threshold | Security or Correctness | `block` — must be fixed before approval |
| ≥ threshold | Performance | `suggest` — surfaces prominently, does not block |
| ≥ threshold | Maintainability / Style | `note` — surfaces as optional, does not block |
| < threshold | Any | Omit from blocking section; may appear in notes |

### 5. Produce `review.md`

Write `.claude/agentic/<branch>/review.md` from `references/templates/review.md`.

- **Blocking findings**: only those with `action: block`
- **Suggestions**: `action: suggest`
- **Notes** (optional section): findings below threshold that are worth mentioning without blocking

### 6. Determine result

- `approved` — no `block` findings
- `changes_requested` — at least one `block` finding

### 7. Update state

In `state.yaml`:
- `phase: finalize` (if approved)
- `phase: implement` + increment `review.attempts` (if changes_requested)
- `roles_done:` append `reviewer`
- `artifacts.review: review.md`
- `review.outcome: approved | changes_requested`
- `review.attempts`: current count

## Output

`review.md` with result and categorized findings. The Implementer reads only the `block` findings.

## Additional Resources

- **`references/confidence-guide.md`** — scoring guidelines per category with examples
