# inenp-pt-dmp-platform-gitops

GitOps manifests for the INENP-PT Data Management Platform — a Kubernetes-based multi-tenant SaaS platform built for the 
MCCE — Cloud Computing Engineering master's programme at FH Burgenland (SS2026), by group inen-pt-group-e. 
This repository is the main documentation entry point for the platform.
## Purpose

This repository is the GitOps source of truth (ArgoCD) for everything deployed into
the platform cluster:

- platform components (ingress-nginx, External Secrets Operator, cert-manager,
  ExternalDNS, Crossplane, CloudNativePG)
- tenant application instances provisioned on-the-fly via Crossplane Compositions

The cluster is bootstrapped by ArgoCD using the **app-of-apps** pattern: `apps/` holds
one ArgoCD `Application` per component, each pointing at its configuration under
`components/`. Changes to cluster state are made exclusively through pull requests.

## Status

Day-1 platform foundation and Day-2 tenant provisioning are in place and verified on the cluster:

| Component | Purpose | Status |
| --- | --- | --- |
| ingress-nginx | public HTTP(S) entry point (GCP L4 LoadBalancer) | deployed |
| External Secrets Operator | sync secrets from GCP Secret Manager via Workload Identity | deployed |
| cert-manager | publicly valid TLS certs via ACME DNS-01 (Let's Encrypt) | deployed |
| ExternalDNS | automated Cloud DNS records from Ingress/Service | deployed |
| Crossplane | on-the-fly tenant provisioning via XRD + Composition | deployed |
| CloudNativePG | in-cluster Postgres operator for per-tenant databases | deployed |

The cluster itself is provisioned via
[`inenp-pt-dmp-platform-iac`](https://github.com/inen-pt-group-e/inenp-pt-dmp-platform-iac).

## Endpoints

- Domain: `mcce-project.xyz` (Google Cloud DNS, delegated)
- Ingress LoadBalancer IP: `35.246.133.66`
- ArgoCD UI: `https://argocd.mcce-project.xyz` (HTTPS via cert-manager + ExternalDNS)
- Tenant apps: `https://<tenant-name>.mcce-project.xyz`

## Repository layout

```text
apps/          # ArgoCD Application manifests (app-of-apps children) - one per component
components/    # component config (Helm values, ClusterIssuers, ClusterSecretStore, ...)
  tenant/      # Crossplane XRD (Tenant CRD) and Composition
tenants/       # Tenant CR instances - one file per tenant
```

## Related repositories

| Repo | Purpose |
| --- | --- |
| [`inenp-pt-dmp-platform-iac`](https://github.com/inen-pt-group-e/inenp-pt-dmp-platform-iac) | Terraform - GKE cluster, networking, IAM, Workload Identity, ArgoCD bootstrap |
| [`inenp-pt-dmp-backend`](https://github.com/inen-pt-group-e/inenp-pt-dmp-backend) | REST API container source |
| [`inenp-pt-dmp-frontend`](https://github.com/inen-pt-group-e/inenp-pt-dmp-frontend) | SPA frontend container source (private) |

## How to add a tenant

1. Open a GitHub issue describing the new tenant.
2. Create `tenants/<name>.yaml` with the `Tenant` CR:

   ```yaml
   apiVersion: platform.mcce-project.xyz/v1alpha1
   kind: Tenant
   metadata:
     name: <name>
   spec:
     database:
       instances: 1
       storageSize: 1Gi
     resources:
       requestsCpu: "500m"
       requestsMemory: "512Mi"
       limitsCpu: "2"
       limitsMemory: "2Gi"
   ```

3. Open a PR; after merge ArgoCD syncs the new `Tenant` CR and Crossplane provisions
   the full stack automatically: namespace, ResourceQuota, Postgres cluster, backend,
   GHCR pull secret, frontend, and Ingress with TLS.
4. Within a few minutes `https://<name>.mcce-project.xyz` is live.

## How to add a platform component

1. Open a GitHub issue describing the change (use the provided templates).
2. Add `components/<name>/` with the Helm values and/or manifests.
3. Add `apps/<name>.yaml` — an ArgoCD `Application` (multi-source: upstream chart +
   `$values`, plus a manifests path if the component ships extra CRs).
4. Open a PR; the lint workflow validates YAML. After merge, ArgoCD app-of-apps picks
   up the new Application and syncs it.

## GenAI Usage

- **Architecture & approach** — planning the platform topology and the app-of-apps /
  Crossplane tenant provisioning model.
- **File / code authoring** — assistance writing Kubernetes manifests, Helm values and
  Crossplane Compositions.
- **Troubleshooting** — debugging GitOps sync, Crossplane provisioning and cluster issues.
- **Text creation** — providing generic text for issues and pull requests.

## Contributing

- Open a GitHub issue using one of the provided templates before starting work
- Branch naming: `<type>/<short-description>` (e.g. `feat/deploy-cert-manager`)
- Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/)
- Every commit/PR references its issue (e.g. `Closes #14`)
- All changes land via squash-merged pull requests with at least one approving review;
  the YAML/Markdown lint workflow must pass
