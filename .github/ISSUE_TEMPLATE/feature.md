---
name: Feature
about: Propose and track a new application or platform configuration change
title: "feat: "
labels: feature
assignees: ""
---

## Context
<!-- Why is this change needed? How does it fit into the overall platform? -->

## Scope
<!-- What components are affected? (e.g. Flux, Crossplane, Helm releases, tenant config, network policies, ...) -->

## Implementation Notes
<!-- Any technical details, constraints, or decisions worth documenting upfront -->


## Acceptance Criteria

- [ ] Manifests / Helm values written and reviewed
- [ ] No hardcoded secrets or plaintext credentials
- [ ] Secrets sourced via External Secrets Operator
- [ ] GitHub Actions pipeline passes (linting / validation)
- [ ] Flux successfully reconciles all affected resources
- [ ] README / docs updated if needed

## Related Issues / PRs
