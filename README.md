# fledge-plugin-github

GitHub commands for [fledge](https://github.com/CorvidLabs/fledge) — view CI checks, issues, and pull requests through the `gh` CLI.

These commands lived in fledge core through v0.14, then moved to this plugin as part of the v0.15 tight-core refactor. The plugin keeps fledge's binary lean for users on GitLab, Gitea, or self-hosted Git, and lets the GitHub-specific surface evolve independently.

## Install

```sh
fledge plugins install CorvidLabs/fledge-plugin-github
```

Requires the [`gh` CLI](https://cli.github.com/) installed and authenticated (`gh auth login`).

## Commands

### `fledge checks [--branch <name>] [--json]`

Show CI/CD check-run status for a branch. Defaults to the current branch.

```
$ fledge checks
* CI checks for main:

  ✅ test (macos-latest)    success    37s
  ✅ test (ubuntu-latest)   success    41s
  ✅ test (windows-latest)  success    2m 22s
  ✅ lint                   success    30s
  ✅ spec-check             success    4s

  5 checks: 5 passed
```

JSON output is the raw GitHub API response from `repos/{owner}/{repo}/commits/{branch}/check-runs`.

### `fledge issues [list | view <num>] [OPTIONS]`

Browse issues. List by default; `view <number>` shows a specific one.

```
$ fledge issues --state all --limit 5
$ fledge issues view 42 --json
```

Options: `--state {open,closed,all}`, `--limit <N>`, `--label <label>`, `--json`.

### `fledge prs [list | view <num>] [OPTIONS]`

Browse pull requests. PR *creation* still lives in core via `fledge work pr` — this plugin is read-only browsing.

```
$ fledge prs --state merged --limit 5
$ fledge prs view 256 --json
```

Options: `--state {open,closed,merged,all}`, `--limit <N>`, `--json`.

## Why a plugin?

`checks`/`issues`/`prs` bake "all dev happens on GitHub" into the surface. A user on GitLab or self-hosted Gitea would carry these as dead weight. The plugin protocol lets the same UX be replicated against any platform — a future `fledge-plugin-gitlab` could register the same `checks`/`issues`/`prs` names against GitLab's API.

## Hacking

Each command is a self-contained POSIX-ish shell script in `bin/`. The scripts use `gh api` (or `gh issue`/`gh pr`) to talk to GitHub, then format the response — no extra runtime dependencies beyond `gh`, `git`, `jq`, and `awk`.

## License

MIT — see [LICENSE](./LICENSE).
