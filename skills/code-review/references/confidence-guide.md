# Code Review — Confidence Scoring Guide

## Confidence scale

| Score | Meaning |
|---|---|
| 90–100 | Verified: the code is objectively wrong, or a known vulnerability |
| 80–89 | High confidence: very likely a bug or security issue based on the code alone |
| 70–79 | Medium: suspicious, but could be intentional or context-dependent |
| < 70 | Low: stylistic preference, speculative, or requires broader context |

## Category examples

### Security (target confidence ≥ 85 to block)

| Finding | Typical confidence |
|---|---|
| SQL string concatenation without parameterization | 95 |
| Password or secret hardcoded in source | 98 |
| Missing `@PreAuthorize` on a mutation endpoint | 88 |
| User-controlled input passed to `Runtime.exec()` | 97 |
| JWT not validated before use | 90 |

### Correctness (target confidence ≥ 80 to block)

| Finding | Typical confidence |
|---|---|
| Off-by-one in a loop boundary | 85 |
| Null dereference with no guard | 88 |
| Missing `await` on a Promise in a critical path | 82 |
| `parseInt` without radix | 72 |
| Race condition in shared mutable state | 80 |
| Exception swallowed silently in a catch block | 75 |

### Performance (suggest, rarely block)

| Finding | Typical confidence |
|---|---|
| N+1 query inside a loop | 85 |
| Unbounded `SELECT *` on a large table | 78 |
| Resource (connection, stream) not closed | 82 |
| O(n²) algorithm where O(n log n) is available | 70 |

### Maintainability / Style (note only)

These should rarely appear in the review unless they are at very high confidence and directly impede correctness. Examples:
- Magic numbers without named constants
- Function doing more than one thing
- Test method name doesn't describe the behavior

## Threshold guidelines

| Threshold | Best for |
|---|---|
| `70` | New codebase or new team member; surface more for learning |
| `80` | Default — good signal-to-noise ratio for mature teams |
| `90` | High-velocity teams who trust their conventions; minimal noise |

## Anti-patterns to avoid

- **Inflating confidence** to make a finding appear more important. Score honestly.
- **Surfacing style preferences** as Security/Correctness with inflated confidence.
- **Softening severity** to avoid conflict. A MAJOR finding that would page someone at 3am stays MAJOR.
- **"Podrías considerar..."** phrasing for blocking findings. If it's blocking, state it plainly.
