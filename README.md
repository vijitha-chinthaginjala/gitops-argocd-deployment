# GitOps Deployment with ArgoCD

Multi-environment Kubernetes delivery using ArgoCD — app-of-apps pattern, Kustomize overlays, RBAC access control, and automated sync with rollback capability.

---

## Architecture Overview

    gitops-argocd-deployment/
    ├── argocd/
    │   ├── app-of-apps.yaml        # Root application — manages all environment apps
    │   ├── application.yml         # Single app reference (legacy)
    │   └── argocd-rbac-cm.yaml     # RBAC policy — role-based access per environment
    ├── apps/
    │   ├── dev/application.yaml    # Dev environment ArgoCD app
    │   ├── staging/application.yaml
    │   └── production/application.yaml  # Production — manual sync only
    ├── overlays/
    │   ├── base/                   # Shared base configuration
    │   ├── dev/                    # Dev overlay — 1 replica, lower resources
    │   ├── staging/                # Staging overlay — 2 replicas, mid resources
    │   └── production/             # Production overlay — 3 replicas, full resources
    └── helm/
        └── templates/              # Base Kubernetes manifests

---

## How This Works

### 1. App-of-Apps Pattern
A single root ArgoCD application watches the `apps/` folder in Git.
When a new environment application YAML is added to `apps/`, ArgoCD automatically
creates that application — no manual UI steps needed.

### 2. Kustomize Overlays
Each environment has its own overlay on top of shared base manifests:

| Environment | Replicas | CPU Request | Memory Request | Auto Sync |
|-------------|----------|-------------|----------------|-----------|
| dev         | 1        | 100m        | 128Mi          | Yes       |
| staging     | 2        | 150m        | 192Mi          | Yes       |
| production  | 3        | 250m        | 256Mi          | Manual    |

### 3. RBAC Access Control
Access is controlled per environment using ArgoCD RBAC policies:

| Role             | Dev          | Staging   | Production |
|------------------|--------------|-----------|------------|
| developer        | sync and get | read only | read only  |
| senior-engineer  | sync and get | sync and get | sync and get |
| everyone else    | read only    | read only | read only  |

Production syncs require a senior engineer — developers cannot trigger production deployments under any circumstances.

### 4. Production Safety
Production application has automated sync deliberately disabled:

- `prune: false` — resources are never auto-deleted in production
- `selfHeal: false` — emergency hotfixes can be made directly without Git revert
- All production changes require manual review and trigger by a senior engineer

---

## How This Maps to Enterprise GitOps

This setup mirrors real-world GitOps practices used in regulated environments:

- **Git as single source of truth** — every environment state is version controlled
- **No direct kubectl access** — all changes go through Git and ArgoCD
- **Least privilege** — developers cannot touch production, ever
- **Audit trail** — every sync, rollback, and override is logged in ArgoCD
- **Environment parity** — same base manifests, environment differences handled by overlays only
- **Safe production** — manual gates prevent accidental automated changes to live systems

---

## Tools Used

- ArgoCD — GitOps continuous delivery
- Kustomize — environment-specific configuration overlays
- Helm — base Kubernetes manifest templating
- Kubernetes — container orchestration (GKE / EKS)
