# Agentic Loop — Phase Detail Reference

## Phase 0: Recall

Search `.claude/knowledge/` before spending tokens on fresh exploration.

```
# Search solutions
ls .claude/knowledge/solutions/
grep -r "<keyword>" .claude/knowledge/solutions/

# Search patterns
grep -r "<keyword>" .claude/knowledge/patterns/

# Search ADRs
ls .claude/knowledge/adrs/
grep -r "<keyword>" .claude/knowledge/adrs/
```

Summarize relevant findings in a "Prior Knowledge" section at the top of `exploration.md`.

Skip gracefully if `.claude/knowledge/` doesn't exist yet.

## Phase 1: Intake — Edge Cases

**Branch naming**: lowercase, hyphens only. Truncate slug at 50 chars.
`issue-23-add-booking-page` not `issue-23-Add_Booking_Page`.

**Worktree already exists**: use `git worktree add <path> <branch>` (no `-b`).

**state.yaml initialization**: write all fields from the template before moving on. Partial state files cause downstream failures.

## Phase 3: Clarification — When to Ask

Surface questions only if they are **blocking**: cannot design or implement without the answer.

Non-blocking questions (ambiguous naming, aesthetic choices) go as notes in `plan.md` — do not pause the loop for them.

Typical blocking questions:
- Scope ambiguity ("does this include the admin panel or just the public view?")
- API contract unknowns ("does the endpoint return 200 or 204 on success?")
- Constraint conflicts ("ADR-0003 says no Redux, but the issue asks for global state")

## Phase 4: Design Gate — Rethink Cycle Limit

Track rethink cycles in `state.yaml` under `design.rethink_count`. After 2 rethinks, stop and escalate to user with a summary of the unresolved constraint.

## Phase 5: Implementation — Harness Fail Protocol

On harness fail:
1. Read the specific error output carefully.
2. Revert only the last change (not all changes).
3. Fix the specific cause.
4. Re-run harness.
5. After 3 consecutive fails on the same step, document as blocker in `state.yaml` and escalate.

## Phase 6: Review Loop — Retry Cap

Track retry count in `state.yaml` under `review.attempts`. After `max_review_retries` (default 2), stop retrying and present the remaining findings to the user for a manual decision.

## Phase 7: Finalize — Commit Message Format

Format: `tipo(scope): descripción breve en español`

Types: `feat`, `fix`, `docs`, `chore`, `refactor`, `test`, `ci`

Examples:
- `feat(web): agregar página de reserva de turnos`
- `fix(backend): corregir validación de email en registro`
- `docs(negocio): actualizar resumen ejecutivo Q3`

## Phase 8: Knowledge Capture — Filter

Only capture if there is genuine new learning. Do not create an ADR for every task.

Capture triggers:
- A non-obvious architectural decision was made (options were weighed)
- A bug's root cause reveals a systemic pattern
- A new constraint was discovered (env/stack/legal)
- A reusable implementation pattern emerged

Skip triggers:
- Routine CRUD work with no novel decisions
- A task that followed an existing ADR exactly
- Documentation-only tasks

## Worktree Cleanup

After the PR is merged (or if the task is abandoned):

```
git worktree remove ../imat-worktrees/issue-<N>-<slug>
git worktree prune
git branch -d issue-<N>-<slug>
```

Do not remove while PR has pending review comments.
