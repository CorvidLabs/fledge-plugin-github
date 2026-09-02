---
id: CHG-0003-complete-truthful-extensionless-coverage-and-lifecycle-scope-for-the-github-plug
state: accepted
type: migration
base_commit: d429bd93845ee8ffc172778967bd7af4039476bb
---

# Complete truthful extensionless coverage and lifecycle scope for the GitHub plugin migration

## Intent

Complete truthful extensionless coverage and lifecycle scope for the GitHub plugin migration

## Affected Canonical Specs

- None

## Acceptance Criteria

- Current SpecSync main measures all six extensionless Bash executables at 100% file and LOC coverage; strict lifecycle validation covers every meaningful rollout file; all four agent integrations, the complete native Fledge lane, Trust doctor, and local Trust verification pass without authenticated GitHub operations.

## No-spec Rationale

This governance correction makes SpecSync measure the six existing extensionless Bash executables and accounts for rollout files omitted from lifecycle scope; it does not change plugin behavior or canonical requirement semantics.
