---
change: CHG-0003-complete-truthful-extensionless-coverage-and-lifecycle-scope-for-the-github-plug
artifact: design
---

# Design

Enable extensionless source discovery in the existing `bin/` source root and
raise the local contract threshold from zero to 100. Keep the six canonical
file entries unchanged.

Replace generic meaningful-path defaults with the repository's actual delivery
surfaces, including all four agent integrations and canonical specs. Record the
eight omitted rollout files in this change's affected paths. Preserve the
immutable Trust and SpecSync consumer pins; the hosted 5.0.1 limitation remains
an explicit release-order blocker rather than being hidden by weaker policy.
