# Changelog

## [v0.6.5] - 2026-05-21

### Fixes

- `repo workspace` now creates clones under `~/.fledge/workspaces/<owner>-<name>-XXXXXX/` instead of `/tmp/fledge-gh-XXXXXX/`. macOS auto-prunes `/tmp` and well-meaning disk-cleanup sweeps wipe it, both of which were destroying agent workspaces mid-task. The new location is stable across reboots and shows up alongside the rest of fledge state.
- `repo workspace-clean` and `repo workspace-push` still accept the legacy `/tmp/fledge-gh-*` prefix so any in-flight workspaces from v0.6.4 and earlier can be pushed or cleaned without manual intervention.
- Override the workspace root with `FLEDGE_WORKSPACES_DIR` for tests / CI / custom layouts.

## [v0.6.4] - 2026-05-21

### Features

- `repo workspace-push <DIR> [-b BRANCH] [-m MSG]` — closes the agent edit-and-PR loop. Optionally checks out / creates a branch, optionally stages + commits any uncommitted changes (when `-m` is given), then `git push -u origin HEAD`. Same `/tmp/fledge-gh-*` safety guard as the other workspace commands. In `--json` mode emits `{branch, repo, path}` so the agent can chain straight into `prs create -R … -H …`.
- `prs create --head <BRANCH>` — wires gh's `--head` through so an agent can create a PR for a branch that lives in a different working tree (the typical post-`workspace-push` case where the cwd isn't the cloned repo).

## [v0.6.3] - 2026-05-21

### Features

- `repo clone <OWNER/NAME> [DIR]` — structured clone command, wraps `gh repo clone` (inherits your `gh auth` for private repos). Optional `--depth N` for shallow clones.
- `repo workspace <OWNER/NAME> [-r REF] [--depth N]` — the higher-level "give me a sandbox" command for agents. Creates a fresh `/tmp/fledge-gh-<owner>-<name>-XXXXXX/<name>` dir, clones the repo there (optionally shallow + at a specific ref), and prints the absolute path on stdout. The `-r REF` is wired through to git's `--branch` so shallow + ref work together.
- `repo workspace-clean <DIR>` — `rm -rf`s a workspace dir. Refuses to remove anything that doesn't live under `/tmp/fledge-gh-`, so a bad agent path can't wipe arbitrary files.

These give agents driven from non-interactive bridges (Discord/Telegram) a structured path to clone a repo for edit work, instead of falling back to `shell-exec git clone ...` which is auto-denied by the Tier 1.5 safety gate in merlin's `--yes` mode.

## [v0.6.2] - 2026-05-21

### Fixes

- `repo view` now also accepts `OWNER/NAME` as a positional argument, matching `gh repo view`'s native syntax. Previously the agent's natural-looking `repo view CorvidLabs/corvid-verify` failed with `unknown argument` and forced an awkward retry with `-R`.
- `repo file` now detects whether the path resolves to a file or a directory and renders accordingly: file → decoded contents (as before), directory → `name<TAB>type<TAB>size` listing. Previously a directory path hit `jq: expected an object but got: array` and the agent had no way to browse remote repos.

## [v0.6.1] - 2026-05-21

### Fixes

- Add `-R, --repo OWNER/NAME` flag to `prs`, `issues`, and `checks` so agents can target any repo without first changing into a clone. Previously every PR / issue subcommand defaulted to `gh`'s cwd-based detection, which silently pointed at the wrong repo when the agent was running in a different working tree — leading to spurious 404s when looking up a PR in a different project.
- `checks -R OWNER/NAME` now falls back to that repo's default branch when `--branch` isn't given, instead of using the local current branch (which has no meaning cross-repo).

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

