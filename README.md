# inenp-pt-dmp-platform-gitops

GitOps manifests for the **INENP-PT Data Management Platform** — a Kubernetes-based multi-tenant SaaS platform built for the FH Burgenland BSWE assignment (SS2026).

## Purpose

This repository contains the GitOps source of truth for everything deployed into the platform cluster:

- platform infrastructure components (ArgoCD, External Secrets Operator, ExternalDNS, cert-manager, ingress-nginx, Crossplane, CloudNativePG)
- tenant application instances provisioned via Crossplane Compositions
- staging tenant for application version testing

Changes to cluster state are made exclusively through pull requests against this repo.

## Status

Bootstrap phase. The cluster is provisioned via [`inenp-pt-dmp-platform-iac`](https://github.com/inen-pt-group-e/inenp-pt-dmp-platform-iac); GitOps tooling and tenant compositions are being added incrementally.

## Related repositories

| Repo | Purpose |
|------|---------|
| [`inenp-pt-dmp-platform-docs`](https://github.com/inen-pt-group-e/inenp-pt-dmp-platform-docs) | Main documentation entry point (architecture, decisions, contributing) |
| [`inenp-pt-dmp-platform-iac`](https://github.com/inen-pt-group-e/inenp-pt-dmp-platform-iac) | Terraform/OpenTofu code that provisions the GKE cluster and platform IAM |
| [`inenp-pt-dmp-backend`](https://github.com/inen-pt-group-e/inenp-pt-dmp-backend) | REST API container source |
| [`inenp-pt-dmp-frontend`](https://github.com/inen-pt-group-e/inenp-pt-dmp-frontend) | SPA frontend container source (private) |

## Documentation

Architecture, design decisions, and contribution guidelines live in [`platform-docs`](https://github.com/inen-pt-group-e/inenp-pt-dmp-platform-docs). Start there if you are new to the project.

## Contributing

- Open a GitHub issue using one of the provided templates before starting work
- Branch naming: `<type>/<issue-nr>-<short-description>` (e.g. `feat/12-bootstrap-argocd`)
- Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/)
- All changes land via squash-merged pull requests with at least one approving review
