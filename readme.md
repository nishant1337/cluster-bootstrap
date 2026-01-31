# 🚀 Kubernetes Cluster Bootstrap with Argo CD (GitOps)

This repo bootstraps a Kubernetes cluster using Argo CD App-of-Apps.
With one `kubectl` command, it installs:

- Argo CD configuration + ingress (HTTPS)
- `ingress-nginx`
- `cert-manager` + `ClusterIssuer` (Let’s Encrypt)
- Portfolio application
- Automatic TLS via `cert-manager`

## ✅ Prerequisites

- Running Kubernetes cluster
- `kubectl` configured
- DNS record pointing to your cluster (example: `argocd.nishantpansuriya.space`)

## 🧩 Step 1: Install Argo CD (one-time, Day 0)

```bash
kubectl create namespace argocd
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Wait until all pods are running:

```bash
kubectl get pods -n argocd
```

## 🌱 Step 2: Bootstrap the entire cluster (ONE command)

This hands over full control to GitOps.

```bash
kubectl apply -f https://raw.githubusercontent.com/nishant1337/cluster-bootstrap/main/root-app.yaml
```

That’s it. Argo CD will now install everything else automatically:
- `ingress-nginx`
- `cert-manager`
- TLS certificates
- Applications

## 🔐 Step 3: Get Argo CD admin password

```bash
kubectl get secret argocd-initial-admin-secret \
  -n argocd \
  -o jsonpath="{.data.password}" | base64 -d && echo
```

### Login credentials
- **Username:** `admin`
- **Password:** output of the command above

## 🌐 Step 4: Access Argo CD UI

Open in browser: [https://argocd.nishantpansuriya.space](https://argocd.nishantpansuriya.space)
(Valid HTTPS via Let’s Encrypt 🎉)

### Verify:
- All applications are **Synced & Healthy**
- `ingress-nginx`, `cert-manager`, `portfolio` are running

## 🧠 How this works (quick)

- `root-app.yaml` → **App-of-Apps**
- Argo CD pulls this repo
- Each app manages its own manifests
- Cluster is fully reproducible
- No `helm install`, no manual `kubectl` applies after bootstrap

## ♻️ Rebuild anytime

You can delete all non-system namespaces and re-run only:

```bash
kubectl apply -f root-app.yaml
```

Git becomes the single source of truth.

## ✅ Result

- HTTPS ingress
- Automated certificates
- Declarative infra
- One-command cluster bootstrap
