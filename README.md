# fledge-plugin-github

GitHub commands for [fledge](https://github.com/CorvidLabs/fledge) — view CI checks, issues, and pull requests through the `gh` CLI.

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

### `fledge github issues [list | view <num>] [OPTIONS]`

Browse issues. List by default; `view <number>` shows a specific one.

```
$ fledge github issues --state all --limit 5
$ fledge github issues view 42 --json
```

Options: `--state {open,closed,all}`, `--limit <N>`, `--label <label>`, `--json`.

### `fledge github prs [list | view <num>] [OPTIONS]`

Browse pull requests. PR creation uses `gh pr create` directly (pure git flow) — this plugin is read-only browsing.

```
$ fledge github prs --state merged --limit 5
$ fledge github prs view 256 --json
```

Options: `--state {open,closed,merged,all}`, `--limit <N>`, `--json`.

## Why a plugin?

A flat `checks`/`issues`/`prs` surface bakes "all dev happens on GitHub" into fledge core. A user on GitLab or self-hosted Gitea would carry these as dead weight. Nesting under `fledge github` keeps the namespace honest and lets a future `fledge-plugin-gitlab` register `fledge gitlab ...` alongside it without colliding.

## Hacking

`bin/fledge-github` is a thin dispatcher; each subcommand is a self-contained POSIX-ish shell script (`bin/fledge-github-checks`, `…-issues`, `…-prs`). The scripts use `gh api` (or `gh issue`/`gh pr`) to talk to GitHub, then format the response — no extra runtime dependencies beyond `gh`, `git`, `jq`, and `awk`.

## License

MIT — see [LICENSE](./LICENSE).
