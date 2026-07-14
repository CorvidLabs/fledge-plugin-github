---
change: CHG-0003-complete-truthful-extensionless-coverage-and-lifecycle-scope-for-the-github-plug
artifact: context
---

# Context

The accepted migration and review-correction changes omitted eight introduced
governance files from their affected paths. SpecSync 5.0.1 therefore rejects
the hosted merge tree even though the native Fledge lane passes.

The repository also contains six extensionless Bash executables under `bin/`.
SpecSync 5.0.1 cannot discover extensionless sources, so the committed
threshold of zero is vacuous. Current SpecSync main adds explicit
`include_extensionless` discovery and can measure the existing canonical
file list without changing runtime code.
