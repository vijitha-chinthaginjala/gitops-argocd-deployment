# GitOps Deployment using ArgoCD

## What This Does
GitOps-based Kubernetes deployment workflow using ArgoCD for automated, version-controlled application delivery with full audit trail, one-click rollback, and automated health-check gating on every release.

## Architecture
Git Push → ArgoCD Detects Change → Sync to Kubernetes → Health Check → Promote or Rollback

## Tech Stack
- **GitOps:** ArgoCD
- **Orchestration:** Kubernetes
- **Packaging:** Helm
- **Monitoring:** Prometheus, Grafana
- **Deployment Strategy:** Rolling updates, Blue-Green

## What It Demonstrates
- Declarative, version-controlled Kubernetes deployments via GitOps
- Automated sync between Git repository and Kubernetes cluster
- One-click rollback to any previous deployment state
- Automated health-check gating before promotion
- Full audit trail of every deployment and change
- Blue-green deployment strategy for zero-downtime releases

## Results
- Reduced failed deployments through automated health-check gating
- One-click rollback capability to any previous state
- Full visibility and audit trail across all deployments
- Zero-downtime releases using blue-green strategy

## How to Run
1. Install ArgoCD in your Kubernetes cluster:
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
2. Apply the ArgoCD application:
kubectl apply -f argocd/application.yml
3. Access ArgoCD UI:
kubectl port-forward svc/argocd-server -n argocd 8080:443
4. ArgoCD will automatically sync and deploy the application

## Project Structure
```
├── argocd/
│   └── application.yml       # ArgoCD application definition
├── helm/
│   ├── Chart.yaml            # Helm chart metadata
│   ├── values.yaml           # Default values
│   ├── values-staging.yaml   # Staging overrides
│   ├── values-production.yaml # Production overrides
│   └── templates/
│       ├── deployment.yaml   # Kubernetes deployment
│       ├── service.yaml      # Kubernetes service
│       └── ingress.yaml      # Kubernetes ingress
└── README.md
```
