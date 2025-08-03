# Evershop Kubernetes GitOps Deployment with Argo CD

This project demonstrates a GitOps-based continuous delivery workflow for deploying the Evershop application to a Kubernetes cluster using **Argo CD**.

---

## 🚀 What This Project Does

- Deploys the Evershop e-commerce platform to Kubernetes
- Uses Argo CD to automatically sync changes from this GitHub repo
- Applies GitOps principles for version-controlled, declarative delivery

---

## 📁 Project Structure


├── k8s/

│ └── evershop.yaml

├── README.md



---

## 🧱 Prerequisites

- Kubernetes cluster (e.g., Minikube, k3d, EKS, etc.)
- `kubectl` CLI access to the cluster
- Argo CD installed in the cluster
- This GitHub repository cloned or forked

---

## 🔧 Setup Steps

### 1️⃣ Install Argo CD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## 2️⃣ Access Argo CD UI
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Visit: http://localhost:8080

Get initial admin password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

## 🎯 Deploy via Argo CD
### Option A: UI
1-Log in to Argo CD

2-Click NEW APP

3-Fill the form:

App name: evershop

Repo URL: https://github.com/<your-username>/evershop-gitops

Path: k8s

Destination:

Cluster: https://kubernetes.default.svc

Namespace: default

Enable Auto-Sync

Click Create

### Option B: Using YAML
Create argocd-app.yaml:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: evershop
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/<your-username>/evershop-gitops
    targetRevision: HEAD
    path: k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

Apply it
```bash
kubectl apply -f argocd-app.yaml
```

## 🔄 GitOps in Action
Whenever you push updates to evershop.yaml in the k8s/ folder, Argo CD will:

Automatically detect the change

Sync it to your cluster

Apply the updated state using GitOps
