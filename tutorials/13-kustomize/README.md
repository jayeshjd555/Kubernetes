# Kustomize Tutorial

This tutorial will guide you through using Kustomize for Kubernetes configuration management.

## Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to access your cluster (v1.14+)
- Basic understanding of Kubernetes concepts
- Optional: Standalone `kustomize` CLI

## Objectives

By the end of this tutorial, you will:
1. Understand Kustomize concepts
2. Create base configurations
3. Create overlays for different environments
4. Use Kustomize transformers
5. Generate ConfigMaps and Secrets
6. Apply configurations with Kustomize

---

## Step 1: Verify Kustomize

### Check kubectl Version

Kustomize is built into kubectl v1.14+:

```bash
kubectl version --client
```

### Test Kustomize

```bash
kubectl kustomize --help
```

### Optional: Install Standalone Kustomize

**macOS:**
```bash
brew install kustomize
```

**Linux:**
```bash
curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh" | bash
```

---

## Step 2: Create Base Configuration

### Step 2.1: Create Directory Structure

```bash
mkdir -p kustomize-demo/base
cd kustomize-demo/base
```

### Step 2.2: Create Deployment

Create `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: nginx:latest
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 200m
            memory: 256Mi
```

### Step 2.3: Create Service

Create `service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

### Step 2.4: Create ConfigMap

Create `configmap.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
  labels:
    app: myapp
data:
  APP_NAME: myapp
  ENV: base
```

### Step 2.5: Create kustomization.yaml

Create `kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - deployment.yaml
  - service.yaml
  - configmap.yaml

commonLabels:
  app: myapp
  managed-by: kustomize

commonAnnotations:
  version: v1.0.0
```

### Step 2.6: Build Base

```bash
kubectl kustomize .
# or
kustomize build .
```

You should see all three resources with common labels and annotations applied.

---

## Step 3: Create Development Overlay

### Step 3.1: Create Overlay Directory

```bash
cd ..
mkdir -p overlays/dev
cd overlays/dev
```

### Step 3.2: Create kustomization.yaml

Create `kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
  - ../../base

namePrefix: dev-

namespace: development

replicas:
  - name: myapp
    count: 1

images:
  - name: nginx
    newTag: latest

configMapGenerator:
  - name: myapp-config
    literals:
      - APP_NAME=myapp
      - ENV=development
      - DEBUG=true
```

### Step 3.3: Build Dev Overlay

```bash
kubectl kustomize .
```

Notice:
- Resources have `dev-` prefix
- Namespace is `development`
- ConfigMap has development values

### Step 3.4: Apply Dev Configuration

```bash
kubectl apply -k .
```

---

## Step 4: Create Staging Overlay

### Step 4.1: Create Staging Directory

```bash
cd ..
mkdir -p staging
cd staging
```

### Step 4.2: Create kustomization.yaml

Create `kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomize

bases:
  - ../../base

namePrefix: staging-

namespace: staging

replicas:
  - name: myapp
    count: 3

images:
  - name: nginx
    newTag: 1.21

resources:
  - hpa.yaml

configMapGenerator:
  - name: myapp-config
    literals:
      - APP_NAME=myapp
      - ENV=staging
      - DEBUG=false
```

### Step 4.3: Create HPA

Create `hpa.yaml`:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

### Step 4.4: Build Staging

```bash
kubectl kustomize .
```

---

## Step 5: Create Production Overlay

### Step 5.1: Create Production Directory

```bash
cd ..
mkdir -p production
cd production
```

### Step 5.2: Create kustomization.yaml

Create `kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
  - ../../base

namePrefix: prod-

namespace: production

replicas:
  - name: myapp
    count: 5

images:
  - name: nginx
    newName: myregistry/nginx
    newTag: 1.21-alpine

resources:
  - hpa.yaml
  - pdb.yaml

configMapGenerator:
  - name: myapp-config
    literals:
      - APP_NAME=myapp
      - ENV=production
      - DEBUG=false

patchesStrategicMerge:
  - resource-patch.yaml
```

### Step 5.3: Create Resource Patch

Create `resource-patch.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  template:
    spec:
      containers:
      - name: myapp
        resources:
          requests:
            cpu: 500m
            memory: 512Mi
          limits:
            cpu: 1000m
            memory: 1Gi
```

### Step 5.4: Create PodDisruptionBudget

Create `pdb.yaml`:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp
spec:
  minAvailable: 3
  selector:
    matchLabels:
      app: myapp
```

### Step 5.5: Build Production

```bash
kubectl kustomize .
```

---

## Step 6: Use Patches

### Step 6.1: Create JSON Patch

Create `patch.json`:

```json
[
  {
    "op": "replace",
    "path": "/spec/replicas",
    "value": 10
  },
  {
    "op": "add",
    "path": "/spec/template/spec/containers/0/env",
    "value": [
      {
        "name": "NEW_VAR",
        "value": "new_value"
      }
    ]
  }
]
```

### Step 6.2: Use JSON Patch in kustomization.yaml

```yaml
patchesJson6902:
  - target:
      group: apps
      version: v1
      kind: Deployment
      name: myapp
    path: patch.json
```

---

## Step 7: Generate ConfigMaps and Secrets

### Step 7.1: Generate ConfigMap from File

Create `config.properties`:

```properties
database.host=localhost
database.port=5432
database.name=myapp
```

Update `kustomization.yaml`:

```yaml
configMapGenerator:
  - name: app-config
    files:
      - config.properties
```

### Step 7.2: Generate Secret

Update `kustomization.yaml`:

```yaml
secretGenerator:
  - name: app-secret
    literals:
      - password=secret123
      - api-key=key456
```

**Note:** Secrets are automatically base64 encoded.

### Step 7.3: Generate from .env File

Create `.env`:

```
DB_PASSWORD=secret123
API_KEY=key456
```

Update `kustomization.yaml`:

```yaml
secretGenerator:
  - name: app-secret
    envs:
      - .env
```

---

## Step 8: Advanced Features

### Step 8.1: Use Multiple Bases

```yaml
resources:
  - ../../base
  - ../../common
  - ../../monitoring
```

### Step 8.2: Use Remote Bases

```yaml
resources:
  - https://github.com/kubernetes/examples/raw/master/guestbook
```

### Step 8.3: Use Name Suffix

```yaml
nameSuffix: -v1
# myapp -> myapp-v1
```

### Step 8.4: Use Common Labels

```yaml
commonLabels:
  environment: production
  team: backend
  app: myapp
```

### Step 8.5: Use Common Annotations

```yaml
commonAnnotations:
  version: v1.0.0
  managed-by: kustomize
  description: Production deployment
```

---

## Step 9: Apply Configurations

### Step 9.1: Apply Base

```bash
cd base
kubectl apply -k .
```

### Step 9.2: Apply Dev Overlay

```bash
cd ../overlays/dev
kubectl apply -k .
```

### Step 9.3: Apply Staging Overlay

```bash
cd ../staging
kubectl apply -k .
```

### Step 9.4: Apply Production Overlay

```bash
cd ../production
kubectl apply -k .
```

---

## Step 10: Verify Deployments

### Step 10.1: Check Dev

```bash
kubectl get all -n development -l app=myapp
```

### Step 10.2: Check Staging

```bash
kubectl get all -n staging -l app=myapp
```

### Step 10.3: Check Production

```bash
kubectl get all -n production -l app=myapp
```

---

## Step 11: Clean Up

### Step 11.1: Delete Dev

```bash
cd overlays/dev
kubectl delete -k .
```

### Step 11.2: Delete Staging

```bash
cd ../staging
kubectl delete -k .
```

### Step 11.3: Delete Production

```bash
cd ../production
kubectl delete -k .
```

---

## Troubleshooting

### Build Errors

1. **Check kustomization.yaml syntax:**
   ```bash
   kubectl kustomize . --dry-run
   ```

2. **Validate YAML:**
   ```bash
   kubectl kustomize . | kubectl apply --dry-run=client -f -
   ```

### Resources Not Found

1. **Check resource paths:**
   ```bash
   ls -la
   ```

2. **Verify base path:**
   ```yaml
   bases:
     - ../../base  # Check this path is correct
   ```

### Patches Not Applying

1. **Check patch syntax:**
   ```bash
   kubectl kustomize . --enable-alpha-plugins
   ```

2. **Verify target resource exists:**
   - Ensure the resource you're patching exists in base

---

## Best Practices

1. **Base Configuration:**
   - Keep base minimal and reusable
   - Don't include environment-specific values

2. **Overlays:**
   - One overlay per environment
   - Use patches for environment-specific changes

3. **Structure:**
   - Follow consistent directory structure
   - Document overlays and patches

4. **Version Control:**
   - Track all kustomization files
   - Use Git for configuration management

5. **Testing:**
   - Always build before applying
   - Validate generated YAML

---

## Next Steps

- Explore advanced transformers
- Learn about Kustomize plugins
- Integrate with GitOps tools (ArgoCD, Flux)
- Use with CI/CD pipelines
- Learn about remote bases

---

## References

- [Kustomize Documentation](https://kustomize.io/)
- [Kustomize Examples](https://github.com/kubernetes-sigs/kustomize/tree/master/examples)
- [Kubectl Kustomize](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)

---

**Happy Kustomizing! 🎨**

