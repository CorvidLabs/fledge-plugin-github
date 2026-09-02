---
spec: github.spec.md
---

## Test Plan

### Integration Tests

- `shellcheck --severity=warning bin/*`
- `bash -n bin/*`
- Run `--help` on every executable.
- Authenticated live reads/mutations remain independently authorized.
