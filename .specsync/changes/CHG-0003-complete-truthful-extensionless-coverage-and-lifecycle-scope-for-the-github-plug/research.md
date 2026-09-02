---
change: CHG-0003-complete-truthful-extensionless-coverage-and-lifecycle-scope-for-the-github-plug
artifact: research
---

# Research

The hosted Trust log identifies exactly eight uncovered meaningful paths:
`.attest.json`, `.augur.toml`, the Claude and Cursor create-change commands
and skills, the Gemini skill, and `AGENTS.md`. These are rollout files already
present in the pull request, not new product behavior.

All six tracked files in `bin/` are extensionless Bash executables and all six
are named by `specs/github/github.spec.md`. Their help surfaces run without
authentication or external mutation. Live GitHub reads and writes cannot be
claimed by this migration because they require separate network credentials
and authorization.

SpecSync main at `a9422aedbe12a3c50787c1fcc074749232f25dfe`
supports explicit extensionless discovery. The consumer action remains pinned
to SpecSync 5.0.1 until an organization-wide pin update is authorized.
