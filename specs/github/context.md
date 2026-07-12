---
spec: github.spec.md
---

## Context

This plugin keeps forge-specific functionality outside fledge core while supporting humans and agents through a consistent nested command surface.

## Related Modules

- GitHub CLI and API
- Git managed workspaces
- Merlin daemon event schema

## Design Decisions

- Delegate authentication and API compatibility to `gh`.
- Bound destructive workspace cleanup by validated roots.
- Keep mutation commands explicit and read commands composable.
