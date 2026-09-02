---
change: CHG-0001-adopt-specsync-5-0-1-and-trust-1-0-0-governance-for-the-github-fledge-plugin
artifact: testing
---

# Testing

- `shellcheck --severity=warning bin/*`
- `bash -n bin/*`
- Help for every `bin/fledge-github*` executable
- `REQ-github-001` evidence is the offline all-executable help smoke; authenticated behavior remains separately authorized.
- `specsync check --strict --force` at advisory threshold 0
- `fledge trust doctor` and `fledge trust verify`
- Authenticated GitHub integration remains independently controlled
