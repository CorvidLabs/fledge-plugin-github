# fledge-plugin-github

[![CI](https://github.com/CorvidLabs/fledge-plugin-github/actions/workflows/ci.yml/badge.svg)](https://github.com/CorvidLabs/fledge-plugin-github/actions/workflows/ci.yml)

GitHub commands for [fledge](https://github.com/CorvidLabs/fledge) — view CI checks, issues, pull requests, and poll for daemon events through the `gh` CLI.

These commands lived in fledge core through v0.14, then moved to this plugin as part of the v0.15 tight-core refactor. The plugin keeps fledge's binary lean for users on GitLab, Gitea, or self-hosted Git, and lets the GitHub-specific surface evolve independently.

## Install

```sh
fledge plugins install CorvidLabs/fledge-plugin-github
```

Requires the [`gh` CLI](https://cli.github.com/) installed and authenticated (`gh auth login`).

## Commands

All commands are nested under `fledge github` so the GitHub-specific surface stays out of the way for users on other forges.

### `fledge github checks [--branch <name>] [--json]`

Show CI/CD check-run status for a branch. Defaults to the current branch.

```
$ fledge github checks
* CI checks for main:

  ✅ test (macos-latest)    success    37s
  ✅ test (ubuntu-latest)   success    41s
  ✅ test (windows-latest)  success    2m 22s
  ✅ lint                   success    30s
  ✅ spec-check             success    4s

  5 checks: 5 passed
```

JSON output is the raw GitHub API response from `repos/{owner}/{repo}/commits/{branch}/check-runs`.

### `fledge github issues [list | view <num> | create | comment <num> | close <num> | reopen <num>] [OPTIONS]`

Browse, create, and write to issues. Lists by default.

```
$ fledge github issues --state all --limit 5
$ fledge github issues view 42 --comments
$ fledge github issues create --title "Bug: something broke"
$ fledge github issues comment 42 -b "Reproduced on 0.3.1."
$ fledge github issues close 42
$ fledge github issues reopen 42
```

List/view options: `--state {open,closed,all}`, `--limit <N>`, `--label <label>`, `--json`, `--comments` (view).  
Create options: `--title <title>`, `--body <body>`, `--label <label>`, `--assignee <login>`, `--json`.  
Comment options: `--body <body>` (required), `--json`.

### `fledge github prs [list | view <num> | create | comment <num> | review <num> | merge <num> | close <num> | reopen <num>] [OPTIONS]`

The full PR surface. Lists by default; everything else takes a PR number.

```
$ fledge github prs --state merged --limit 5
$ fledge github prs view 256 --diff --comments
$ fledge github prs create --fill
$ fledge github prs create --ai --draft
$ fledge github prs comment 256 -b "Looks good — one nit inline."
$ fledge github prs review 256 --approve
$ fledge github prs review 256 --request-changes -b "See comments."
$ fledge github prs merge 256 --squash
$ fledge github prs close 256
```

List/view options: `--state {open,closed,merged,all}`, `--limit <N>`, `--json`, `--diff` (view, append unified diff), `--comments` (view, append conversation).  
Create options: `--title <title>`, `--body <body>`, `--base <branch>`, `--draft`, `--fill`, `--ai`, `--json`.  
Comment options: `--body <body>` (required), `--json`.  
Review options: one of `--approve` / `--request-changes` / `--comment-review`, plus `--body <body>` (required for the latter two).  
Merge options: `--squash` (default) | `--merge` | `--rebase`.

`--ai` generates a PR title and body by sending your commits and diff to `fledge ask`. It shows a preview and lets you confirm, edit, or abort before creating the PR.

### `fledge github repo [view | file <path>] [OPTIONS]`

Read repo metadata or fetch a file's contents at a ref. `file` wraps `gh api repos/.../contents/...` and decodes the base64, so it works for any branch/tag/SHA without needing a clone or a raw-URL token.

```
$ fledge github repo view
$ fledge github repo view -R CorvidLabs/merlin --json
$ fledge github repo file CHANGELOG.md
$ fledge github repo file Cargo.toml -r v0.3.0
$ fledge github repo file README.md -R CorvidLabs/fledge-plugin-github
```

Options: `-R, --repo <owner/name>` (default: current repo), `-r, --ref <ref>` (file only), `--json`.

### `fledge github poll [OPTIONS]`

Poll for new issues and PRs as structured event objects. Designed for [Merlin](https://github.com/CorvidLabs/merlin) daemon mode — the output matches the daemon `Event` schema.

```
$ fledge github poll --repo CorvidLabs/merlin --types issues --limit 3
$ fledge github poll --since issues/100
$ fledge github poll --types issues --label bug
```

Options: `--repo <owner/repo>`, `--since <ID>` (e.g. `issues/42`), `--limit <N>`, `--types {issues,prs}`, `--label <label>`, `--state {open,closed,all}`.

Output is a JSON array of event objects with fields: `source`, `repo`, `event_type`, `id`, `title`, `labels`, `author`, `body`, `url`, `timestamp`.

## Why a plugin?

A flat `checks`/`issues`/`prs` surface bakes "all dev happens on GitHub" into fledge core. A user on GitLab or self-hosted Gitea would carry these as dead weight. Nesting under `fledge github` keeps the namespace honest and lets a future `fledge-plugin-gitlab` register `fledge gitlab ...` alongside it without colliding.

## Hacking

`bin/fledge-github` is a thin dispatcher; each subcommand is a self-contained POSIX-ish shell script (`bin/fledge-github-checks`, `…-issues`, `…-prs`, `…-poll`, `…-repo`). The scripts use `gh api` (or `gh issue`/`gh pr`/`gh repo`) to talk to GitHub, then format the response — no extra runtime dependencies beyond `gh`, `git`, `python3`, `awk`, and `base64`.

## License

MIT — see [LICENSE](./LICENSE).
