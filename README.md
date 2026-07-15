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

## GHCR image pull access

The chart references an image pull secret named `ghcr-pull-secret` so Kubernetes can pull
`ghcr.io/orenamir2/argocd-demo:1.0.0` from GHCR.

Create a GitHub token with `read:packages` access, then create the secret in each application
namespace:

```sh
export GITHUB_USER=<your-github-username>
export GHCR_TOKEN=<github-token-with-read-packages>

for namespace in demo-dev demo-staging demo-production; do
  kubectl create secret docker-registry ghcr-pull-secret \
    --namespace "$namespace" \
    --docker-server=ghcr.io \
    --docker-username="$GITHUB_USER" \
    --docker-password="$GHCR_TOKEN" \
    --docker-email="$GITHUB_USER@users.noreply.github.com" \
    --dry-run=client -o yaml | kubectl apply -f -
done
```
