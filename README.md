# Kubernetes Homelab Setup

## Bootstrap

### Install Argo CD

Non HA installation
Releases: https://github.com/argoproj/argo-cd/releases

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.6.2/manifests/install.yaml
```

### Configure Client

```bash
brew install argocd
```

```bash
kubectl config set-context --current --namespace=argocd

argocd login --core
```

### Enable Argo CD configurations

```bash
argocd app create argocd-config \
  --repo https://github.com/tkohn/homelab-k8s-apps.git \
  --path operation-applications/argocd-config \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace argocd
```

### Enable Operation Applications

```bash
argocd app create argocd \
  --repo https://github.com/tkohn/homelab-k8s-apps.git \
  --path operation-applications/argocd \
  --project operation-applications \
  --revision HEAD \
  --sync-policy automated \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace argocd
```

## Access UI

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

https://localhost:8080/

username: admin

initial password:

```bash
argocd admin initial-password -n argocd

# or via
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Delete initial password:

```bash
kubectl delete secret -n argocd argocd-initial-admin-secret
```