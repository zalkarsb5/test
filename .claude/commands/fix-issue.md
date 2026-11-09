---
description: Find issue references in codebase, show context, and prepare a fix plan
allowed-tools: Bash, Read, Grep, Glob
---

Find all references to issue #$ARGUMENTS in the codebase.

Steps:
1. Search for "#$ARGUMENTS", "ISSUE-$ARGUMENTS", "TODO.*$ARGUMENTS"
   across .js, .ts, .py, .go files
2. Show each match with ±5 lines context
3. Identify files that need changes
4. Find associated test file
5. Suggest plan: failing test → fix → verify
