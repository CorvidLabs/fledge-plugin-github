# Changelog

## [v0.6.0] - 2026-05-21

### Features

- `prs view`: add `--diff` and `--comments` flags to append the unified diff and conversation comments + reviews to the view output.
- `prs comment <NUMBER> -b BODY`: post a comment on a pull request.
- `prs review <NUMBER>`: submit a review — one of `--approve`, `--request-changes`, or `--comment-review`, plus `--body`.
- `prs merge <NUMBER>`: merge a PR with `--squash` (default), `--merge`, or `--rebase`.
- `prs close <NUMBER>` / `prs reopen <NUMBER>`: close without merging or reopen.
- `issues view`: add `--comments` flag to append the issue conversation.
- `issues comment <NUMBER> -b BODY`: post a comment on an issue.
- `issues close <NUMBER>` / `issues reopen <NUMBER>`.
- New `repo` subcommand:
  - `repo view [-R OWNER/NAME] [--json]` — repo metadata.
  - `repo file <PATH> [-R OWNER/NAME] [-r REF] [--json]` — read a file's contents at any ref via `gh api repos/.../contents/...`. Closes the gap where agents fell back to authenticated-failing `web-fetch` against `raw.githubusercontent.com`.

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

