---
id: CHG-0002-correct-specsync-agent-guidance-and-governance-path-coverage
state: accepted
type: documentation
base_commit: f5ee59c46fc03e38cf2c3b183c6a139a9b79c46c
---

# Correct SpecSync agent guidance and governance path coverage

## Intent

Correct SpecSync agent guidance and governance path coverage

## Affected Canonical Specs

- None

## Acceptance Criteria

- Claude
- Cursor
- and Gemini classify the complete remaining create-spec input before choosing a module name; Gemini create-change shell-escapes the displayed raw arguments exactly once; the SDD policy covers Trust
- Fledge
- Augur
- Attest
- documentation
- and plugin manifests; the canonical changelog row has a valid version; native verification and strict SpecSync 100% coverage pass.

## No-spec Rationale

Correct generated agent instructions, SDD meaningful-path coverage, and canonical changelog formatting without changing GitHub plugin requirements or runtime behavior.
