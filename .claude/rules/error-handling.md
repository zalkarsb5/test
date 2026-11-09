# Error Handling Rules

## Principle
Every operation that can fail must handle its failure gracefully.

## Rules
1. Always use try-catch for async/await
2. Define custom errors (FetchError, ValidationError)
3. Never swallow errors silently — handle or re-throw
4. Validate inputs early
5. Add context to errors: what failed, why, what was the input

## Python Equivalent
Use try/except with custom exception classes. Always chain with `from err`.
