# inenp-pt-dmp-platform-gitops

GitOps manifests for the **INENP-PT Data Management Platform** — a Kubernetes-based
multi-tenant SaaS platform built for the FH Burgenland BSWE assignment (SS2026).
This repository is the **main documentation entry point** for the platform.

## Purpose

This repository is the GitOps source of truth (ArgoCD) for everything deployed into
the platform cluster:

- platform components (ingress-nginx, External Secrets Operator, cert-manager,
  ExternalDNS, and — upcoming — Crossplane, CloudNativePG)
- tenant application instances provisioned via Crossplane Compositions (upcoming)

The cluster is bootstrapped by ArgoCD using the **app-of-apps** pattern: `apps/` holds
one ArgoCD `Application` per component, each pointing at its configuration under
`components/`. Changes to cluster state are made exclusively through pull requests.

## Status

Day-1 platform foundation is in place and verified on the cluster:

| Component | Purpose | Status |
| --- | --- | --- |
| ingress-nginx | public HTTP(S) entry point (GCP L4 LoadBalancer) | deployed |
| External Secrets Operator | sync secrets from GCP Secret Manager via Workload Identity | deployed |
| cert-manager | publicly valid TLS certs via ACME DNS-01 (Let's Encrypt) | deployed |
| ExternalDNS | automated Cloud DNS records from Ingress/Service | deployed |
| Crossplane | on-the-fly tenant provisioning | planned |
| CloudNativePG | in-cluster Postgres for tenants | planned |

The cluster itself is provisioned via
[`inenp-pt-dmp-platform-iac`](https://github.com/inen-pt-group-e/inenp-pt-dmp-platform-iac).

## Endpoints

- Domain: `mcce-project.xyz` (Google Cloud DNS, delegated)
- Ingress LoadBalancer IP: `35.246.133.66`
- ArgoCD UI: `https://argocd.mcce-project.xyz` (HTTPS via cert-manager + ExternalDNS)

## Repository layout

```text
apps/          # ArgoCD Application manifests (app-of-apps children) - one per component
components/    # component config (Helm values, ClusterIssuers, ClusterSecretStore, ...)
```

## Related repositories

| Repo | Purpose |
| --- | --- |
| [`inenp-pt-dmp-platform-iac`](https://github.com/inen-pt-group-e/inenp-pt-dmp-platform-iac) | Terraform/OpenTofu - GKE cluster, networking, IAM, Workload Identity, ArgoCD bootstrap |
| [`inenp-pt-dmp-backend`](https://github.com/inen-pt-group-e/inenp-pt-dmp-backend) | REST API container source |
| [`inenp-pt-dmp-frontend`](https://github.com/inen-pt-group-e/inenp-pt-dmp-frontend) | SPA frontend container source (private) |

## How to add a platform component

1. Open a GitHub issue describing the change (use the provided templates).
2. Add `components/<name>/` with the Helm values and/or manifests.
3. Add `apps/<name>.yaml` - an ArgoCD `Application` (multi-source: upstream chart +
   `$values`, plus a manifests path if the component ships extra CRs).
4. Open a PR; the lint workflow validates YAML. After merge, ArgoCD app-of-apps picks
   up the new Application and syncs it.

## Contributing

- Open a GitHub issue using one of the provided templates before starting work
- Branch naming: `<type>/<short-description>` (e.g. `feat/deploy-cert-manager`)
- Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/)
- Every commit/PR references its issue (e.g. `Closes #14`)
- All changes land via squash-merged pull requests with at least one approving review;
  the YAML/Markdown lint workflow must pass
