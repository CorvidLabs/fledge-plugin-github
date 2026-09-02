---
change: CHG-0002-correct-specsync-agent-guidance-and-governance-path-coverage
artifact: context
---

# Context

The installed create-spec prompts chose the first token as a module name before deciding whether the complete input was a natural-language description. Gemini's create-change prompt also referenced a shell variable that is not provided by its template. Separately, the SDD policy did not classify the committed Trust, Fledge, Augur, Attest, documentation, manifest, or agent-integration files as meaningful.

These are governance and instruction defects. The plugin's command behavior and canonical requirements remain unchanged.
