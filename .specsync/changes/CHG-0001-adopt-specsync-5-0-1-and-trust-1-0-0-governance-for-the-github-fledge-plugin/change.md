---
id: CHG-0001-adopt-specsync-5-0-1-and-trust-1-0-0-governance-for-the-github-fledge-plugin
state: implementing
type: migration
base_commit: e18318d0d12e1cf4d3f85dcab343b3ea6f1bfb8d
---

# Adopt SpecSync 5.0.1 and Trust 1.0.0 governance for the GitHub Fledge plugin

## Intent

Adopt SpecSync 5.0.1 and Trust 1.0.0 governance for the GitHub Fledge plugin

## Affected Canonical Specs

- `github`

## Acceptance Criteria

- SpecSync strict check passes at explicit advisory threshold 0; all four integrations report installed; Trust doctor and verification pass.
- ShellCheck, Bash syntax, and help for every GitHub plugin executable remain green; live GitHub mutations remain independently authorized.

## No-spec Rationale

Not applicable
