# Changelog

## [v0.5.0] - 2026-05-13

### Features

- Add `fledge github poll` subcommand for daemon event polling (#5)
  - Polls GitHub for new issues and PRs via the `gh` CLI
  - Outputs JSON array of Event objects matching the Merlin daemon schema
  - Supports `--since`, `--types`, `--label`, `--repo`, `--limit`, `--state` options
  - Uses python3 for JSON transformation

## [v0.2.1] - 2026-04-25

### Other

- Fix: resolve dispatcher symlink to find sibling helpers (f6dd786)

## [v0.2.0] - 2026-04-25

### Features

- initial scaffold — checks, issues, prs (663d27e)

### Fixes

- add missing plugin.toml manifest (37e9680)

### Other

- Bump: version 0.2.0 (671593a)
- Refactor: nest commands under `fledge github` (a43948f)

