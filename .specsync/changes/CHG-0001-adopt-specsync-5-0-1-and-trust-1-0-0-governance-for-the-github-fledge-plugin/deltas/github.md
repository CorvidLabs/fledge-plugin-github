## MODIFIED

### REQUIREMENT REQ-github-001

The plugin SHALL provide the documented PR, issue, repository, checks, and poll subcommands through authenticated `gh`.

Acceptance Criteria
- Offline help succeeds for every `bin/fledge-github*` executable without creating external state.
- Authenticated GitHub reads and mutations remain separately authorized.
