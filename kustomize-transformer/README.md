# Kubernetes Kustomize — Dev & Stage Environment Setup

A step-by-step guide to managing Kubernetes manifests for multiple environments (`dev` and `stage`) using **Kustomize** — no Helm required.

---

## 📁 Project Structure

```
kustomize/
├── base/
│   ├── deploy.yml
│   └── kustomization.yml
└── overlays/
    ├── dev/
    │   └── kustomization.yml
    └── stage/
        └── kustomization.yml
```

---

## 🚀 Step-by-Step Implementation

### Step 1 — Create the Project Directory

```bash
mkdir kustomize
cd kustomize
```

---

### Step 2 — Create the Base Directory & Files

```bash
mkdir base
cd base
```

**`base/deploy.yml`** — Base Nginx Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

**`base/kustomization.yml`** — Base Kustomization:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
- deploy.yml
```

> ⚠️ **Important:** The file must be named `kustomization.yml` (not `kustomize.yml`). Kustomize only recognizes `kustomization.yaml`, `kustomization.yml`, or `Kustomization`.

---

### Step 3 — Verify the Base

```bash
cd ..
kubectl kustomize base
```

Expected output:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx-deployment
spec:
  replicas: 3
  ...
```

---

### Step 4 — Create the Overlays

```bash
mkdir -p overlays/dev overlays/stage
```

**`overlays/dev/kustomization.yml`** — Dev overlay (uses `nginx:latest` and prefixes name with `dev-`):

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
  - ../../base

namePrefix: dev-

images:
  - name: nginx
    newTag: latest
```

**`overlays/stage/kustomization.yml`** — Stage overlay (uses `nginx:1.14.2` and prefixes name with `stage-`):

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
  - ../../base

namePrefix: stage-
```

---

### Step 5 — Preview Overlays (Dry Run)

```bash
# Preview dev environment
kubectl kustomize overlays/dev/

# Preview stage environment
kubectl kustomize overlays/stage/
```

---

### Step 6 — Create Namespaces

```bash
kubectl create ns dev
kubectl create ns stage
```

---

### Step 7 — Apply to Cluster

```bash
# Deploy to dev namespace
kubectl apply -k overlays/dev/ -n dev

# Deploy to stage namespace
kubectl apply -k overlays/stage/ -n stage
```

---

### Step 8 — Verify Deployments

```bash
# Check dev namespace
kubectl get deployments -n dev

# Check stage namespace
kubectl get deployments -n stage

# Check all namespaces
kubectl get deployments -A
```

---

### Step 9 — Push to GitHub

```bash
cd ~/kustomize

# Initialize git repo
git init .

# Add a .gitignore (optional but recommended)
echo ".DS_Store" > .gitignore

# Stage all files
git add .

# Commit
git commit -m "feat: add kustomize base and overlays for dev and stage"

# Add your GitHub remote (replace with your repo URL)
git remote add origin https://github.com/<your-username>/<your-repo>.git

# Push
git branch -M main
git push -u origin main
```

---

## 🔑 Key Concepts

| Concept | Description |
|---|---|
| **Base** | Common manifests shared across all environments |
| **Overlay** | Environment-specific patches/overrides on top of base |
| **namePrefix** | Prepends a string to all resource names (e.g., `dev-nginx-deployment`) |
| **images** | Overrides the container image tag per environment |
| `kubectl kustomize` | Renders the final YAML without applying it (dry run) |
| `kubectl apply -k` | Applies the kustomized manifests to the cluster |

---

## 🐛 Common Mistakes

- Naming the kustomization file `kustomize.yml` instead of `kustomization.yml` — Kustomize won't find it.
- Placing `kustomization.yml` in the wrong directory (e.g., `overlays/` instead of `overlays/dev/`).
- Using `cd ...` instead of `cd ../..` to go up two directories.

---

## 📚 References

- [Kustomize Official Docs](https://kustomize.io/)
- [kubectl kustomize reference](https://kubectl.docs.kubernetes.io/references/kustomize/)
