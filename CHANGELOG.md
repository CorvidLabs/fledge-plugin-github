# Changelog

All notable changes are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versions follow [Semantic Versioning](https://semver.org/) once we tag 1.0.

## [v0.6.6] — 2026-08-15

### Fixes

- `prs create --json` and `issues create --json` no longer always fail. Both
  appended `--json number,url,title` directly to `gh pr create` / `gh issue
  create`, but neither `gh` subcommand accepts `--json` (only `list`/`view`
  do) — every JSON-mode create exited with `unknown flag: --json` before
  creating anything. Worse, retrying the identical command without `--json`
  looked like the obvious fix and silently succeeded, publishing a real
  PR/issue with whatever title/body was passed — a footgun for anything
  scripting a retry. Fixed the same way `prs comment`/`issues comment`
  already handle this: create without `--json`, parse the number out of the
  URL `gh` prints on stdout, then fetch `{number, url, title}` from `gh pr
  view` / `gh issue view`. (#15)

## [v0.6.5] — 2026-05-21

### Fixes

- `repo workspace` now creates clones under `~/.fledge/workspaces/<owner>-<name>-XXXXXX/` instead of `/tmp/fledge-gh-XXXXXX/`. macOS auto-prunes `/tmp` and well-meaning disk-cleanup sweeps wipe it, both of which were destroying agent workspaces mid-task. The new location is stable across reboots.
- `workspace-clean` and `workspace-push` still accept the legacy `/tmp/fledge-gh-*` prefix so in-flight workspaces from v0.6.4 aren't orphaned by the upgrade.
- Override the workspace root with `FLEDGE_WORKSPACES_DIR` for tests / CI / custom layouts.

## [v0.6.4] — 2026-05-21

### Features

- `repo workspace-push <DIR> [-b BRANCH] [-m MSG]` — closes the edit-and-PR loop. Optionally checks out / creates a branch, optionally stages + commits any uncommitted changes (when `-m` is given), then `git push -u origin HEAD`. With `--json` emits `{branch, repo, path}` so callers can chain straight into `prs create -R … -H …`.
- `prs create --head <BRANCH>` — wires gh's `--head` through so a PR can be opened for a branch that lives in a different working tree (the typical post-`workspace-push` case).

## [v0.6.3] — 2026-05-21

### Features

A structured path for cloning a repo for edit work, instead of falling back to `shell-exec git clone …` which is auto-denied by the Tier 1.5 safety gate in merlin's `--yes` mode.

- `repo clone <OWNER/NAME> [DIR]` — thin wrapper over `gh repo clone`, inherits your `gh auth` for private repos. Optional `--depth N` for shallow clones.
- `repo workspace <OWNER/NAME> [-r REF] [--depth N]` — higher-level "give me a sandbox". Creates a fresh dir, clones the repo there (optionally shallow + at a specific ref via git's `--branch`), and prints the absolute path on stdout.
- `repo workspace-clean <DIR>` — `rm -rf` with a safety guard that refuses anything not under the workspace root.

## [v0.6.2] — 2026-05-21

### Fixes

- `repo view` now also accepts `OWNER/NAME` as a positional, matching `gh repo view`'s native syntax. Previously the natural-looking `repo view CorvidLabs/corvid-verify` failed with `unknown argument`.
- `repo file` now detects whether PATH resolves to a file or a directory and renders accordingly: file → decoded contents (as before); directory → `name<TAB>type<TAB>size` listing. Previously a directory path hit `jq: expected an object but got: array`.

## [v0.6.1] — 2026-05-21

### Fixes

- Add `-R, --repo OWNER/NAME` flag to `prs`, `issues`, and `checks` so callers can target any repo without first changing into a clone. Previously each subcommand defaulted to `gh`'s cwd-based detection, which silently pointed at the wrong repo and gave spurious 404s.
- `checks -R OWNER/NAME` now falls back to that repo's default branch when `--branch` isn't given, instead of using the local current branch.

## [v0.6.0] — 2026-05-21

### Features

Expanded the read-only surface (`checks`, `issues view/list/create`, `prs view/list/create`) with the write ops and repo-content access that real workflows actually need.

- `prs view`: `--diff` and `--comments` flags to append the unified diff and conversation comments + reviews.
- `prs comment <NUMBER> -b BODY`, `prs merge <NUMBER> [--squash|--merge|--rebase]`, `prs close <NUMBER>`, `prs reopen <NUMBER>`.
- `prs review <NUMBER>` — submit a review (`--approve` / `--request-changes` / `--comment-review`, plus `--body`).
- `issues view --comments`, `issues comment <NUMBER> -b BODY`, `issues close <NUMBER>`, `issues reopen <NUMBER>`.
- New `repo` subcommand:
  - `repo view [-R OWNER/NAME] [--json]` — repo metadata.
  - `repo file <PATH> [-R OWNER/NAME] [-r REF] [--json]` — file contents at any ref, via `gh api repos/.../contents/...`. Closes the gap where consumers had to `web-fetch raw.githubusercontent.com` URLs that fail on auth.

## [v0.5.0] — 2026-05-13

### Features

- Add `fledge github poll` subcommand for daemon event polling (#5). Polls GitHub for new issues and PRs via the `gh` CLI; outputs a JSON array of Event objects matching the Merlin daemon schema. Supports `--since`, `--types`, `--label`, `--repo`, `--limit`, `--state`.

## [v0.2.1] — 2026-04-25

### Fixes

- Resolve dispatcher symlink to find sibling helpers (f6dd786).

## [v0.2.0] — 2026-04-25

### Features

- Initial scaffold — `checks`, `issues`, `prs` (663d27e).
- Nest commands under `fledge github` (a43948f).

### Fixes

- Add missing `plugin.toml` manifest (37e9680).
