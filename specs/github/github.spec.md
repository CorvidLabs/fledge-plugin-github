---
module: github
version: 2
status: active
files:
  - bin/fledge-github
  - bin/fledge-github-checks
  - bin/fledge-github-issues
  - bin/fledge-github-poll
  - bin/fledge-github-prs
  - bin/fledge-github-repo

db_tables: []
depends_on: []
---

# Github

## Purpose

Provide a nested fledge GitHub surface over authenticated `gh` and Git: pull request and issue operations, check status, repository reads/clones, safety-scoped managed workspaces, and structured event polling.

## Public API

| Surface | Behavior |
|---------|----------|
| pull requests | List/view/create/comment/review/merge/close/reopen, including cross-repository targeting and JSON. |
| issues | List/view/create/comment/close/reopen, including labels, assignees, comments, cross-repository targeting, and JSON. |
| repository | View metadata, read files/directories at refs, clone, and manage bounded workspaces. |
| checks | Read check-run status for a branch or target repository. |
| poll | Emit structured issue/PR events for daemon consumers. |
| dispatcher | Route nested commands and provide stable help/usage. |

## Invariants

1. Cross-repository `-R` targeting is forwarded consistently to supported subcommands.
2. Read-only JSON modes preserve raw/structured API data without human formatting.
3. Mutating PR/issue/review/merge operations require explicit subcommands and arguments.
4. AI-assisted PR creation requires user confirmation/edit/abort before opening a PR.
5. Workspace cleanup refuses paths outside the configured workspace root or recognized legacy prefix.
6. Workspace push operates only inside a validated managed workspace and reports its branch/repository/path.
7. File reads decode files and list directories without writing repository state.
8. Poll output retains the daemon event schema and deterministic ordering/cursor semantics.

## Behavioral Examples

```
Given a managed workspace path outside the configured workspace root
When workspace-clean is requested
Then the command refuses deletion and exits non-zero
```

## Error Cases

| Error | When | Behavior |
|-------|------|----------|
| Missing gh/authentication | A GitHub API command runs without usable `gh` auth | Surface the `gh` failure and exit non-zero. |
| Invalid subcommand/options | CLI input does not match the selected surface | Print scoped usage and exit non-zero. |
| Missing mutation body/title | Create/comment/review requires absent content | Reject before calling GitHub. |
| Unsafe workspace path | Push/clean path is not recognized as managed | Refuse the operation. |
| Missing repository/ref/path | API lookup cannot resolve input | Surface the GitHub response without local mutation. |
| Failed checks | Read check status contains failures | Report aggregate failed state while preserving individual results. |

## Dependencies

- authenticated GitHub CLI and Git
- Bash, Python 3, awk, and base64
- fledge AI command only for explicitly selected AI-assisted PR creation

## Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1 | 2026-07-12 | Document existing GitHub command and workspace safety behavior for SpecSync 5 adoption. |
| 2026-07-13 | CHG-0001-adopt-specsync-5-0-1-and-trust-1-0-0-governance-for-the-github-fledge-plugin: Adopt SpecSync 5.0.1 and Trust 1.0.0 governance for the GitHub Fledge plugin |
