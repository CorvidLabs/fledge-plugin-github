# fledge-plugin-github

[![CI](https://github.com/CorvidLabs/fledge-plugin-github/actions/workflows/ci.yml/badge.svg)](https://github.com/CorvidLabs/fledge-plugin-github/actions/workflows/ci.yml)

GitHub commands for [fledge](https://github.com/CorvidLabs/fledge) — list/view/create/comment/review/merge pull requests, list/view/create/comment/close issues, read repo files at any ref, clone repos into managed workspaces, view CI checks, and poll for daemon events — all through the `gh` CLI.

These commands lived in fledge core through v0.14, then moved to this plugin as part of the v0.15 tight-core refactor. The plugin keeps fledge's binary lean for users on GitLab, Gitea, or self-hosted Git, and lets the GitHub-specific surface evolve independently.

## Install

```sh
fledge plugins install CorvidLabs/fledge-plugin-github
```

Requires the [`gh` CLI](https://cli.github.com/) installed and authenticated (`gh auth login`).

## Commands

All commands are nested under `fledge github` so the GitHub-specific surface stays out of the way for users on other forges. **Every PR/issue/checks subcommand accepts `-R OWNER/NAME`** to target a repo other than the one in the current directory.

### `fledge github prs` — list, view, create, comment, review, merge, close, reopen

```
fledge github prs [list]                          List open PRs (default)
fledge github prs view <NUMBER>                   View a specific PR
fledge github prs create                          Create a new PR
fledge github prs comment <NUMBER> -b BODY        Post a comment
fledge github prs review <NUMBER> ...             Submit a review
fledge github prs merge <NUMBER> [--squash|...]   Merge the PR
fledge github prs close <NUMBER>                  Close without merging
fledge github prs reopen <NUMBER>                 Reopen a closed PR
```

**List / view options:** `--state {open,closed,merged,all}`, `--limit <N>`, `-R OWNER/NAME`, `--json`. On `view`: `--diff` appends the unified diff, `--comments` appends conversation comments + reviews.

**Create options:** `-t/--title`, `-b/--body`, `--base BRANCH`, `-H/--head BRANCH`, `--draft`, `--fill`, `--ai`, `-R OWNER/NAME`, `--json`. `--ai` generates the title and body by sending your commits and diff to `fledge ask`; you confirm, edit, or abort before the PR is opened.

**Comment options:** `-b/--body` (required), `-R OWNER/NAME`, `--json`.

**Review options:** one of `--approve` / `--request-changes` / `--comment-review`, plus `-b/--body` (required for the latter two). `-R OWNER/NAME` for cross-repo reviews.

**Merge options:** `--squash` (default), `--merge`, or `--rebase`. `-R OWNER/NAME` works here too.

```sh
$ fledge github prs --state merged --limit 5
$ fledge github prs view 256 --diff --comments
$ fledge github prs view 5 -R CorvidLabs/corvid-verify --json
$ fledge github prs create --ai --draft
$ fledge github prs create -R CorvidLabs/corvid-verify -H fix/jwks-kid -t "Fix kid mismatch" -b "..."
$ fledge github prs comment 256 -b "Looks good — one nit inline."
$ fledge github prs review 256 --request-changes -b "See comments."
$ fledge github prs merge 256 --squash
```

### `fledge github issues` — list, view, create, comment, close, reopen

```
fledge github issues [list]                       List open issues (default)
fledge github issues view <NUMBER>                View a specific issue
fledge github issues create                       Create a new issue
fledge github issues comment <NUMBER> -b BODY     Post a comment
fledge github issues close <NUMBER>               Close
fledge github issues reopen <NUMBER>              Reopen
```

**List / view options:** `--state {open,closed,all}`, `--limit <N>`, `--label LABEL`, `-R OWNER/NAME`, `--json`. On `view`: `--comments` appends the issue's conversation.

**Create options:** `-t/--title`, `-b/--body`, `--label LABEL`, `-a/--assignee LOGIN`, `-R OWNER/NAME`, `--json`.

**Comment options:** `-b/--body` (required), `-R OWNER/NAME`, `--json`.

```sh
$ fledge github issues --state all --limit 50
$ fledge github issues view 42 --comments
$ fledge github issues create --title "Bug: something broke" --label bug
$ fledge github issues comment 42 -b "Reproduced on 0.3.1 too."
$ fledge github issues close 42
```

### `fledge github repo` — view, file, clone, workspace, workspace-push, workspace-clean

```
fledge github repo [view] [OWNER/NAME]            View repo metadata (default)
fledge github repo file <PATH>                    Read a file OR list a directory
fledge github repo clone <OWNER/NAME> [DIR]       Clone via `gh repo clone`
fledge github repo workspace <OWNER/NAME>         Clone into a managed workspace
fledge github repo workspace-push <DIR>           Commit (if -m) + push the workspace branch
fledge github repo workspace-clean <DIR>          rm -rf a workspace dir (safety-checked)
```

**Common options:** `-R OWNER/NAME` (view/clone/workspace also accept it as a positional), `-r/--ref REF` (file lookup; workspace checkout), `--depth N` (shallow clone — clone + workspace), `--json`.

**`repo view`** prints the repo's metadata (default-branch, description, languages, visibility, etc.). With `--json` you get the raw GitHub API shape.

**`repo file <PATH>`** is the do-everything read tool: when PATH is a file it returns the decoded contents; when PATH is a directory it returns a `name<TAB>type<TAB>size` listing. Pass `-r REF` to read at any branch / tag / SHA. Works against private repos via `gh auth`.

**`repo clone`** is a thin wrapper over `gh repo clone OWNER/NAME [DIR]`.

**`repo workspace`** is the higher-level "give me a sandbox" command — designed for agents (and humans) who want a throwaway local checkout to make edits in. Creates a fresh directory under `~/.fledge/workspaces/<owner>-<name>-XXXXXX/<name>/`, clones the repo there (optionally shallow with `--depth`, optionally checked out at a specific ref with `-r`), and prints the absolute path on stdout. `--json` emits `{path, repo, ref}` instead.

Override the workspace root with the `FLEDGE_WORKSPACES_DIR` environment variable for tests, CI, or custom layouts. (Workspaces created by v0.6.4 and earlier under `/tmp/fledge-gh-*` are still recognized by `workspace-push` and `workspace-clean` so in-flight directories don't get orphaned by the upgrade.)

**`repo workspace-push <DIR>`** closes the edit loop. Optional flags:

- `-b/--branch <NAME>` — check out / create a branch in the workspace before pushing (handy when the agent wants to PR a feature branch off the default branch the workspace was cloned onto).
- `-m/--message <MSG>` — when given, stages all changes (`git add -A`) and commits with that message before pushing. Skips the commit if the tree is already clean, so re-runs are idempotent.

Then runs `git push -u origin <current-branch>`. With `--json` you get `{branch, repo, path}` back so you can chain straight into `prs create -R <repo> -H <branch>`.

**`repo workspace-clean <DIR>`** `rm -rf`s the workspace dir — and walks up to remove the unique parent so no empty shells are left behind. Refuses any path that isn't under the workspace root (or the legacy `/tmp/fledge-gh-*` prefix), so a wrong path can't wipe arbitrary files.

```sh
$ fledge github repo view CorvidLabs/merlin
$ fledge github repo file CHANGELOG.md
$ fledge github repo file Sources -R CorvidLabs/corvid-verify --ref feat/jwks-endpoint
$ fledge github repo file Cargo.toml -r v0.3.0
$ fledge github repo clone CorvidLabs/corvid-verify
$ fledge github repo workspace CorvidLabs/corvid-verify -r feat/jwks-endpoint --depth 1
$ fledge github repo workspace-push ~/.fledge/workspaces/... -b fix/kid -m "Add kid claim"
$ fledge github repo workspace-clean ~/.fledge/workspaces/CorvidLabs-corvid-verify-abc123
```

### The five-step edit-and-PR loop

Putting the workspace commands together for agents (or humans) who want to fix a bug in a repo other than their cwd:

```sh
# 1. Clone into a managed workspace.
WS=$(fledge github repo workspace CorvidLabs/corvid-verify -r feat/jwks-endpoint --depth 1)

# 2. Edit files under $WS — vi, an LLM, a script, whatever.
$EDITOR "$WS/Sources/CorvidVerify/Services/JwtTokenService.swift"

# 3. Commit + push to a new feature branch.
fledge github repo workspace-push "$WS" -b fix/jwks-kid -m "Add kid claim to JWT signing"

# 4. Open the PR for that branch.
fledge github prs create -R CorvidLabs/corvid-verify -H fix/jwks-kid \
    -t "Fix JWKS kid mismatch" -b "Closes the upstream auth break."

# 5. Clean the workspace.
fledge github repo workspace-clean "$WS"
```

### `fledge github checks` — view CI/CD check status

```sh
$ fledge github checks
* CI checks for main:

  ✅ test (macos-latest)    success    37s
  ✅ test (ubuntu-latest)   success    41s
  ✅ test (windows-latest)  success    2m 22s
  ✅ lint                   success    30s
  ✅ spec-check             success    4s

  5 checks: 5 passed
```

**Options:** `-b/--branch NAME` (default: current branch, or the target repo's default branch when `-R` is set), `-R/--repo OWNER/NAME`, `--json` (raw GitHub `repos/{owner}/{repo}/commits/{branch}/check-runs` response).

```sh
$ fledge github checks --branch main
$ fledge github checks -R CorvidLabs/merlin --branch main
$ fledge github checks --json | jq '.check_runs[].name'
```

### `fledge github poll` — daemon event polling

```sh
$ fledge github poll --repo CorvidLabs/merlin --types issues --limit 3
$ fledge github poll --since issues/100
$ fledge github poll --types issues --label bug
```

Polls GitHub for new issues and PRs as structured event objects. Designed for [Merlin](https://github.com/CorvidLabs/merlin) daemon mode — the output matches the daemon `Event` schema.

**Options:** `--repo OWNER/NAME`, `--since <ID>` (e.g. `issues/42`), `--limit N`, `--types {issues,prs}`, `--label LABEL`, `--state {open,closed,all}`.

Output is a JSON array of event objects with fields: `source`, `repo`, `event_type`, `id`, `title`, `labels`, `author`, `body`, `url`, `timestamp`.

## Why a plugin?

A flat `checks`/`issues`/`prs` surface bakes "all dev happens on GitHub" into fledge core. A user on GitLab or self-hosted Gitea would carry these as dead weight. Nesting under `fledge github` keeps the namespace honest and lets a future `fledge-plugin-gitlab` register `fledge gitlab ...` alongside it without colliding.

## Hacking

`bin/fledge-github` is a thin dispatcher; each subcommand is a self-contained POSIX-ish shell script (`bin/fledge-github-checks`, `…-issues`, `…-prs`, `…-repo`, `…-poll`). The scripts use `gh api` (or `gh issue`/`gh pr`/`gh repo`) to talk to GitHub, then format the response — no extra runtime dependencies beyond `gh`, `git`, `python3`, `awk`, and `base64`.

CI runs `bash -n` syntax checks and `shellcheck --severity=warning` against every script in `bin/`.

## License

MIT — see [LICENSE](./LICENSE).
