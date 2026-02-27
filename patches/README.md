# Patches

This directory demonstrates two methods for patching Kubernetes resources using Kustomize:

1. **YAML patch approach** – using native Kubernetes resource structure in patch file.
2. **JSON patch approach** – using a strategic merge or JSON patch syntax (shown below).

Both approaches modify the `replicas` field of a `Deployment` defined in `base/deploy.yml`.

---

## Project Structure

```
patches/
├── base/
│   ├── deploy.yml            # original deployment
│   └── kustomization.yml     # base kustomization referencing deploy.yml
└── overlays/
    ├── dev/
    │   └── replicas.yml      # YAML patch example (dev uses YAML)
    |   └── kustomization.yml
    └── stage/
        └── replicas.yml      # JSON patch example (stage uses JSON)
        └── kustomization.yml
```

> The YAML file in `overlays/dev/replicas.yml` contains a direct Kubernetes patch while the
> JSON file in `overlays/stage/replicas.yml` uses the JSON patch syntax (`op: replace`).

## Step‑by‑step usage

1. **Navigate to the overlay you want to work with**

   ```bash
   cd patches/overlays/dev
   # or
   cd patches/overlays/stage
   ```

2. **Inspect the patch file**

   - YAML approach (`dev`):
     ```yaml
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: nginx-deployment
     spec:
       replicas: 2           # new value in dev overlay
     ```
   - JSON approach (`stage`):
     ```yaml
     # JSON approach
     - op: replace
       path: /spec/replicas
       value: 4
     ```

3. **Build the targeted manifest with `kubectl kustomize`**

   ```bash
   # show resulting YAML, no apply
   kubectl kustomize overlays/dev
   kubectl kustomize overlays/stage
   ```

4. **Apply the overlay to your cluster**

   ```bash
   kubectl apply -k overlays/dev   # apply dev overlay
   kubectl apply -k overlays/stage # apply stage overlay
   ```

5. **Verify the deployment**

   ```bash
   kubectl get deployment nginx-deployment -o yaml
   # check the 'spec.replicas' field updated accordingly
   ```

## Notes

- Using overlays allows you to maintain a common base and apply environment-specific
  changes.
- You may mix patch types; Kustomize supports YAML strategic merge patches and JSON
  patches interchangeably by listing them under `patches:` in the overlay's
  `kustomization.yml`.

---

This README is intended to clarify how the patches in this repository work and how
to exercise them with Kustomize and `kubectl` commands.