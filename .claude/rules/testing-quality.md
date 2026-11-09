---
paths: ["tests/**", "**/*.test.js", "**/*.test.ts", "**/*.spec.*"]
---

# Testing Quality Rules

## Pattern: Arrange-Act-Assert (AAA)
Every test follows AAA: set up data → call function → verify result.

## Coverage targets
- ≥80% statement coverage
- ≥75% branch coverage
- Every public function: happy path + at least one error case

## Factory functions for test data
Use `createMockUser(overrides)` pattern instead of hardcoded objects.

## Priority: happy path → error cases → edge cases
