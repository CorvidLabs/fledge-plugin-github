---
spec: github.spec.md
---

## User Stories

- As a developer, I want GitHub PR, issue, repository, and check operations under one fledge namespace.
- As an agent, I want bounded workspaces and structured JSON/event output for safe automation.

## Acceptance Criteria

### REQ-github-001

The plugin SHALL provide the documented PR, issue, repository, checks, and poll subcommands through authenticated `gh`.

### REQ-github-002

Supported operations SHALL accept explicit cross-repository targeting and JSON output.

### REQ-github-003

Workspace push and cleanup SHALL reject paths outside recognized managed workspace roots.

### REQ-github-004

AI-assisted PR creation SHALL require confirmation, editing, or abort before creating external state.

### REQ-github-005

Poll output SHALL preserve structured daemon event fields and cursor filtering.

## Constraints

- Requires authenticated `gh`, Git, Bash, and network access for live operations.

## Out of Scope

- Non-GitHub forges and automatic mutation without explicit command/arguments.
