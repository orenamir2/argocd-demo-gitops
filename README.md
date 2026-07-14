# argocd-demo-gitops

GitOps repository for deploying `ghcr.io/orenamir2/argocd-demo` with Argo CD and Helm.

## Layout

```text
argocd-demo-gitops/
├── charts/argocd-demo/          # Helm chart
├── environments/                # Per-environment Helm values
├── applications/                # One Argo CD Application per environment
├── projects/                    # Argo CD AppProject definitions
└── applicationsets/             # ApplicationSet-driven environment generation
```

## Render locally

```sh
helm lint charts/argocd-demo
helm template argocd-demo charts/argocd-demo --values environments/dev-values.yaml
helm template argocd-demo charts/argocd-demo --values environments/staging-values.yaml
helm template argocd-demo charts/argocd-demo --values environments/production-values.yaml
```

## Bootstrap Argo CD

Apply the project first, then either the individual applications or the ApplicationSet:

```sh
kubectl apply -f projects/demo-team.yaml
kubectl apply -f applications/
```

or:

```sh
kubectl apply -f projects/demo-team.yaml
kubectl apply -f applicationsets/environments.yaml
```
