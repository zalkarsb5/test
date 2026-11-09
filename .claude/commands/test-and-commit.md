---
description: Run tests, check linting, and prepare files for commit
allowed-tools: Bash, Read, Glob
---

Run the full pre-commit check sequence:

1. Run test suite: npm test. Show summary (passed/failed/skipped).
2. Run linter: npm run lint. Show errors/warnings.
3. If tests or linting fail — show errors, suggest fixes. STOP.
4. If everything passes — run git status --short.
5. Suggest conventional commit message (feat/fix/refactor/test/docs).
6. Show ready-to-use git commands.
