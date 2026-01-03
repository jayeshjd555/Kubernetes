# Flux Tutorial

This tutorial will guide you through setting up and using Flux for GitOps continuous delivery in Kubernetes.

## Prerequisites

- A running Kubernetes cluster (v1.19+)
- `kubectl` configured to access your cluster
- Admin access to the cluster
- A Git repository (GitHub, GitLab, or any Git server)
- Basic understanding of Kubernetes and Git

## Objectives

By the end of this tutorial, you will:
1. Install Flux in your cluster
2. Bootstrap Flux to your Git repository
3. Create GitRepository and Kustomization resources
4. Set up automatic synchronization
5. Configure image automation
6. Understand Flux concepts

---

## Step 1: Install Flux CLI

### Step 1.1: Install Flux CLI

**macOS:**
```bash
brew install fluxcd/tap/flux
```

**Linux:**
```bash
curl -s https://fluxcd.io/install.sh | sudo bash
```

**Windows:**
```powershell
iwr https://fluxcd.io/install.ps1 -UseBasicParsing | iex
```

### Step 1.2: Verify Installation

```bash
flux --version
```

---

## Step 2: Bootstrap Flux

### Step 2.1: Prerequisites Check

```bash
flux check --pre
```

This checks:
- Kubernetes cluster access
- Cluster configuration
- RBAC permissions

### Step 2.2: Bootstrap to GitHub

**Using GitHub Personal Access Token:**
```bash
export GITHUB_TOKEN=your_github_token

flux bootstrap github \
  --owner=yourusername \
  --repository=myrepo \
  --branch=main \
  --path=clusters/my-cluster \
  --personal
```

**Using SSH:**
```bash
flux bootstrap github \
  --owner=yourusername \
  --repository=myrepo \
  --branch=main \
  --path=clusters/my-cluster \
  --ssh-hostname=github.com \
  --private-key-file=~/.ssh/id_rsa
```

### Step 2.3: Bootstrap to GitLab

```bash
export GITLAB_TOKEN=your_gitlab_token

flux bootstrap gitlab \
  --owner=yourgroup \
  --repository=myrepo \
  --branch=main \
  --path=clusters/my-cluster \
  --token-auth
```

### Step 2.4: Bootstrap to Generic Git

```bash
flux bootstrap git \
  --url=https://github.com/yourusername/myrepo \
  --branch=main \
  --path=clusters/my-cluster \
  --username=yourusername \
  --password=yourpassword
```

### Step 2.5: Verify Bootstrap

```bash
# Check Flux components
kubectl get pods -n flux-system

# Check Flux resources
flux get sources git
flux get kustomizations
```

---

## Step 3: Repository Structure

### Step 3.1: Recommended Structure

```
myrepo/
├── clusters/
│   └── my-cluster/
│       └── flux-system/
│           ├── gotk-components.yaml
│           ├── gotk-sync.yaml
│           └── kustomization.yaml
├── apps/
│   └── myapp/
│       ├── base/
│       │   ├── deployment.yaml
│       │   ├── service.yaml
│       │   └── kustomization.yaml
│       └── overlays/
│           ├── dev/
│           │   └── kustomization.yaml
│           └── production/
│               └── kustomization.yaml
└── infrastructure/
    └── monitoring/
        └── prometheus/
```

### Step 3.2: Create Base Application

**apps/myapp/base/deployment.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  namespace: default
spec:
  replicas: 2
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
```

**apps/myapp/base/service.yaml:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
  namespace: default
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

**apps/myapp/base/kustomization.yaml:**
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - deployment.yaml
  - service.yaml
```

---

## Step 4: Create GitRepository

### Step 4.1: Create GitRepository Resource

Create `clusters/my-cluster/apps.yaml`:

```yaml
apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: GitRepository
metadata:
  name: myrepo
  namespace: flux-system
spec:
  interval: 1m
  url: https://github.com/yourusername/myrepo
  ref:
    branch: main
  secretRef:
    name: git-credentials
```

### Step 4.2: Create Secret for Git Access

**For HTTPS:**
```bash
kubectl create secret generic git-credentials \
  --from-literal=username=yourusername \
  --from-literal=password=yourpassword \
  -n flux-system
```

**For SSH:**
```bash
kubectl create secret generic git-credentials \
  --from-file=identity=~/.ssh/id_rsa \
  --from-file=known_hosts=~/.ssh/known_hosts \
  -n flux-system
```

### Step 4.3: Apply GitRepository

```bash
kubectl apply -f clusters/my-cluster/apps.yaml
```

### Step 4.4: Verify GitRepository

```bash
# Check status
flux get sources git

# Check details
kubectl describe gitrepository myrepo -n flux-system
```

---

## Step 5: Create Kustomization

### Step 5.1: Create Kustomization Resource

Create `clusters/my-cluster/myapp-kustomization.yaml`:

```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: myapp
  namespace: flux-system
spec:
  interval: 5m
  path: ./apps/myapp/base
  prune: true
  sourceRef:
    kind: GitRepository
    name: myrepo
  healthChecks:
    - apiVersion: apps/v1
      kind: Deployment
      name: myapp
      namespace: default
    - apiVersion: v1
      kind: Service
      name: myapp
      namespace: default
```

### Step 5.2: Apply Kustomization

```bash
kubectl apply -f clusters/my-cluster/myapp-kustomization.yaml
```

### Step 5.3: Verify Kustomization

```bash
# Check status
flux get kustomizations

# Check details
kubectl describe kustomization myapp -n flux-system

# Check reconciliation
flux reconcile kustomization myapp
```

### Step 5.4: Verify Deployment

```bash
kubectl get pods -l app=myapp
kubectl get svc myapp
```

---

## Step 6: Configure Automatic Sync

### Step 6.1: Update Kustomization

The Kustomization already has `interval: 5m`, which means it will sync every 5 minutes automatically.

### Step 6.2: Test Auto-Sync

1. Make a change in Git repository
2. Push the change
3. Wait for sync interval (5 minutes)
4. Or trigger manual reconciliation:
   ```bash
   flux reconcile kustomization myapp
   ```

### Step 6.3: Force Reconciliation

```bash
# Reconcile source
flux reconcile source git myrepo

# Reconcile kustomization
flux reconcile kustomization myapp
```

---

## Step 7: Use Helm Charts

### Step 7.1: Create HelmRepository

```yaml
apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: HelmRepository
metadata:
  name: bitnami
  namespace: flux-system
spec:
  interval: 10m
  url: https://charts.bitnami.com/bitnami
```

Apply:
```bash
kubectl apply -f helm-repository.yaml
```

### Step 7.2: Create HelmRelease

```yaml
apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: nginx
  namespace: default
spec:
  interval: 5m
  chart:
    spec:
      chart: nginx
      sourceRef:
        kind: HelmRepository
        name: bitnami
        namespace: flux-system
      version: '13.0.0'
  values:
    replicaCount: 2
    service:
      type: ClusterIP
```

Apply:
```bash
kubectl apply -f helm-release.yaml
```

### Step 7.3: Verify HelmRelease

```bash
# Check status
flux get helmreleases

# Check details
kubectl describe helmrelease nginx
```

---

## Step 8: Set Up Image Automation

### Step 8.1: Create ImageRepository

```yaml
apiVersion: image.toolkit.fluxcd.io/v1beta2
kind: ImageRepository
metadata:
  name: myapp
  namespace: flux-system
spec:
  image: docker.io/yourusername/myapp
  interval: 1m
```

Apply:
```bash
kubectl apply -f image-repository.yaml
```

### Step 8.2: Create ImagePolicy

```yaml
apiVersion: image.toolkit.fluxcd.io/v1beta1
kind: ImagePolicy
metadata:
  name: myapp
  namespace: flux-system
spec:
  imageRepositoryRef:
    name: myapp
  policy:
    semver:
      range: '1.x'
```

Apply:
```bash
kubectl apply -f image-policy.yaml
```

### Step 8.3: Create ImageUpdateAutomation

```yaml
apiVersion: image.toolkit.fluxcd.io/v1beta1
kind: ImageUpdateAutomation
metadata:
  name: myapp
  namespace: flux-system
spec:
  interval: 5m
  sourceRef:
    kind: GitRepository
    name: myrepo
  git:
    checkout:
      ref:
        branch: main
    commit:
      author:
        name: Flux
        email: flux@example.com
      messageTemplate: 'Update image: {{range .Updated.Images}}{{println .}}{{end}}'
  update:
    path: ./apps/myapp/base
    strategy: Setters
```

Apply:
```bash
kubectl apply -f image-update-automation.yaml
```

### Step 8.4: Verify Image Automation

```bash
# Check image repository
flux get image repository myapp

# Check image policy
flux get image policy myapp

# Check image update automation
flux get image update myapp
```

---

## Step 9: Multi-Environment Setup

### Step 9.1: Create Dev Kustomization

```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: myapp-dev
  namespace: flux-system
spec:
  interval: 5m
  path: ./apps/myapp/overlays/dev
  prune: true
  sourceRef:
    kind: GitRepository
    name: myrepo
  dependsOn:
    - name: infrastructure
      namespace: flux-system
```

### Step 9.2: Create Production Kustomization

```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: myapp-prod
  namespace: flux-system
spec:
  interval: 10m
  path: ./apps/myapp/overlays/production
  prune: true
  sourceRef:
    kind: GitRepository
    name: myrepo
  dependsOn:
    - name: myapp-dev
      namespace: flux-system
```

---

## Step 10: Configure Notifications

### Step 10.1: Create Provider

**Slack Provider:**
```yaml
apiVersion: notification.toolkit.fluxcd.io/v1beta2
kind: Provider
metadata:
  name: slack
  namespace: flux-system
spec:
  type: slack
  channel: deployments
  secretRef:
    name: slack-url
```

**Create Secret:**
```bash
kubectl create secret generic slack-url \
  --from-literal=address=https://hooks.slack.com/services/YOUR/WEBHOOK/URL \
  -n flux-system
```

### Step 10.2: Create Alert

```yaml
apiVersion: notification.toolkit.fluxcd.io/v1beta2
kind: Alert
metadata:
  name: myapp-alert
  namespace: flux-system
spec:
  providerRef:
    name: slack
  eventSeverity: info
  eventSources:
    - kind: GitRepository
      name: myrepo
    - kind: Kustomization
      name: myapp
```

Apply:
```bash
kubectl apply -f alert.yaml
```

---

## Step 11: Troubleshooting

### Kustomization Not Syncing

1. **Check status:**
   ```bash
   flux get kustomizations
   kubectl describe kustomization myapp -n flux-system
   ```

2. **Check GitRepository:**
   ```bash
   flux get sources git
   kubectl describe gitrepository myrepo -n flux-system
   ```

3. **Reconcile manually:**
   ```bash
   flux reconcile kustomization myapp
   ```

### Image Automation Not Working

1. **Check ImageRepository:**
   ```bash
   flux get image repository myapp
   ```

2. **Check ImagePolicy:**
   ```bash
   flux get image policy myapp
   ```

3. **Check ImageUpdateAutomation:**
   ```bash
   flux get image update myapp
   ```

### Repository Access Issues

1. **Check secret:**
   ```bash
   kubectl get secret git-credentials -n flux-system
   ```

2. **Update secret:**
   ```bash
   kubectl delete secret git-credentials -n flux-system
   kubectl create secret generic git-credentials \
     --from-literal=username=yourusername \
     --from-literal=password=yourpassword \
     -n flux-system
   ```

---

## Step 12: Clean Up

### Step 12.1: Delete Kustomization

```bash
kubectl delete kustomization myapp -n flux-system
```

### Step 12.2: Uninstall Flux

```bash
# Delete all Flux resources
flux uninstall

# Or manually
kubectl delete -f clusters/my-cluster/flux-system/
```

---

## Best Practices

1. **Use Bootstrap:**
   - Always use `flux bootstrap`
   - Let Flux manage itself
   - Store Flux config in Git

2. **Repository Structure:**
   - Use recommended structure
   - Separate apps and infrastructure
   - Use overlays for environments

3. **Dependencies:**
   - Define clear dependencies
   - Use `dependsOn` for sequencing
   - Test order

4. **Health Checks:**
   - Define health checks
   - Monitor application health
   - Set up alerts

5. **Image Automation:**
   - Use semantic versioning
   - Define update policies
   - Review automated commits

---

## Next Steps

- Set up multi-cluster Flux
- Configure advanced image policies
- Set up multiple notification channels
- Implement GitOps best practices
- Integrate with CI pipeline

---

## References

- [Flux Documentation](https://fluxcd.io/docs/)
- [Flux Examples](https://github.com/fluxcd/flux2-kustomize-helm-example)
- [Flux Best Practices](https://fluxcd.io/docs/guides/repository-structure/)

---

**Happy GitOps with Flux! 🚀**

