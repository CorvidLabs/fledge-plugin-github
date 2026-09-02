---
change: CHG-0003-complete-truthful-extensionless-coverage-and-lifecycle-scope-for-the-github-plug
artifact: testing
---

# Testing

Run ShellCheck at warning severity, Bash syntax validation, and offline
`--help` for all six executables through `fledge lanes run verify`.

Run current SpecSync main with strict forced validation at 100% coverage and
confirm six of six files and all executable LOC are measured. Confirm Claude,
Cursor, Codex, and Gemini integrations are installed. Run `fledge trust
doctor` and local `fledge trust verify` with current SpecSync main first on
`PATH`.

Do not execute or claim authenticated GitHub reads, comments, reviews, merges,
workspace pushes, or other external mutations.
