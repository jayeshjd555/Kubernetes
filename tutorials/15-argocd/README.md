# ArgoCD Tutorial

This tutorial will guide you through setting up and using ArgoCD for GitOps continuous delivery in Kubernetes.

## Prerequisites

- A running Kubernetes cluster (v1.19+)
- `kubectl` configured to access your cluster
- Admin access to the cluster
- A Git repository (GitHub, GitLab, or any Git server)
- Basic understanding of Kubernetes and Git

## Objectives

By the end of this tutorial, you will:
1. Install ArgoCD in your cluster
2. Access ArgoCD web UI
3. Create applications in ArgoCD
4. Set up automatic sync
5. Understand ArgoCD concepts
6. Manage multi-environment deployments

---

## Step 1: Install ArgoCD

### Step 1.1: Create Namespace

```bash
kubectl create namespace argocd
```

### Step 1.2: Install ArgoCD

**Option 1: Using kubectl (Recommended)**
```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**Option 2: Using Helm**
```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm install argocd argo/argo-cd \
  --namespace argocd \
  --create-namespace
```

### Step 1.3: Wait for Pods to be Ready

```bash
kubectl wait --for=condition=ready pod --all -n argocd --timeout=300s
```

### Step 1.4: Verify Installation

```bash
# Check pods
kubectl get pods -n argocd

# Check services
kubectl get svc -n argocd
```

You should see pods like:
- `argocd-server-xxx`
- `argocd-application-controller-xxx`
- `argocd-repo-server-xxx`
- `argocd-redis-xxx`

---

## Step 2: Access ArgoCD

### Step 2.1: Port-Forward to ArgoCD Server

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

### Step 2.2: Get Initial Admin Password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

Save this password - you'll need it to login.

### Step 2.3: Access Web UI

Open your browser and go to:
```
https://localhost:8080
```

**Note:** You may need to accept the self-signed certificate warning.

**Login:**
- Username: `admin`
- Password: (the password from step 2.2)

### Step 2.4: Install ArgoCD CLI (Optional)

**macOS:**
```bash
brew install argocd
```

**Linux:**
```bash
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
```

**Login via CLI:**
```bash
argocd login localhost:8080
```

---

## Step 3: Prepare Git Repository

### Step 3.1: Create Sample Application

Create a simple Kubernetes application in your Git repository:

**deployment.yaml:**
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

**service.yaml:**
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

**namespace.yaml:**
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: myapp
```

### Step 3.2: Commit to Git

```bash
git add .
git commit -m "Add sample application"
git push
```

---

## Step 4: Create Application in ArgoCD

### Step 4.1: Using Web UI

1. Click **"New App"** button
2. Fill in application details:
   - **Application Name:** `myapp`
   - **Project:** `default`
   - **Sync Policy:** `Automatic`
   - **Repository URL:** `https://github.com/yourusername/yourrepo`
   - **Revision:** `HEAD`
   - **Path:** `./` (or path to your manifests)
   - **Cluster URL:** `https://kubernetes.default.svc`
   - **Namespace:** `default`
3. Click **"Create"**

### Step 4.2: Using CLI

```bash
argocd app create myapp \
  --repo https://github.com/yourusername/yourrepo \
  --path ./ \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --sync-policy automated \
  --self-heal \
  --auto-prune
```

### Step 4.3: Using YAML

Create `application.yaml`:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/yourusername/yourrepo
    targetRevision: HEAD
    path: ./
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

Apply:
```bash
kubectl apply -f application.yaml
```

---

## Step 5: Sync Application

### Step 5.1: Check Application Status

**Using CLI:**
```bash
argocd app get myapp
```

**Using Web UI:**
- Go to Applications
- Click on `myapp`
- View status and resources

### Step 5.2: Manual Sync (if not automated)

**Using CLI:**
```bash
argocd app sync myapp
```

**Using Web UI:**
- Click on application
- Click "Sync" button
- Review changes
- Click "Synchronize"

### Step 5.3: Verify Deployment

```bash
kubectl get pods -l app=myapp
kubectl get svc myapp
```

---

## Step 6: Configure Automatic Sync

### Step 6.1: Update Application

Edit application to enable auto-sync:

```bash
argocd app set myapp --sync-policy automated --self-heal --auto-prune
```

Or update YAML:

```yaml
spec:
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

### Step 6.2: Test Auto-Sync

1. Make a change in Git repository
2. Push the change
3. ArgoCD will automatically detect and sync
4. Check application status in UI

---

## Step 7: Create Multi-Environment Setup

### Step 7.1: Create Project

Create `project.yaml`:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: myproject
  namespace: argocd
spec:
  description: My project for multi-environment
  sourceRepos:
    - 'https://github.com/yourusername/yourrepo'
  destinations:
    - namespace: 'dev'
      server: https://kubernetes.default.svc
    - namespace: 'staging'
      server: https://kubernetes.default.svc
    - namespace: 'production'
      server: https://kubernetes.default.svc
```

Apply:
```bash
kubectl apply -f project.yaml
```

### Step 7.2: Create Dev Application

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp-dev
  namespace: argocd
spec:
  project: myproject
  source:
    repoURL: https://github.com/yourusername/yourrepo
    targetRevision: HEAD
    path: ./kustomize/overlays/dev
  destination:
    server: https://kubernetes.default.svc
    namespace: dev
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### Step 7.3: Create Production Application

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp-prod
  namespace: argocd
spec:
  project: myproject
  source:
    repoURL: https://github.com/yourusername/yourrepo
    targetRevision: HEAD
    path: ./kustomize/overlays/production
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy: {}  # Manual sync for production
```

---

## Step 8: Use Helm Charts

### Step 8.1: Create Helm-based Application

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp-helm
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://charts.example.com
    chart: myapp
    targetRevision: 1.0.0
    helm:
      valueFiles:
        - values.yaml
      values: |
        replicaCount: 3
        image:
          tag: v1.2.3
  destination:
    server: https://kubernetes.default.svc
    namespace: default
```

---

## Step 9: Use Sync Waves

### Step 9.1: Create Resources with Waves

**namespace.yaml:**
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: myapp
  annotations:
    argocd.argoproj.io/sync-wave: "0"
```

**configmap.yaml:**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
  namespace: myapp
  annotations:
    argocd.argoproj.io/sync-wave: "1"
data:
  config: value
```

**deployment.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  namespace: myapp
  annotations:
    argocd.argoproj.io/sync-wave: "2"
spec:
  # ...
```

This ensures:
1. Namespace created first (wave 0)
2. ConfigMap created second (wave 1)
3. Deployment created last (wave 2)

---

## Step 10: Use Hooks

### Step 10.1: Create Pre-Sync Hook

**pre-sync-job.yaml:**
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pre-sync-migration
  namespace: default
  annotations:
    argocd.argoproj.io/hook: PreSync
    argocd.argoproj.io/hook-delete-policy: HookSucceeded
spec:
  template:
    spec:
      containers:
      - name: migration
        image: myapp:migrate
        command: ["/bin/sh", "-c", "run-migrations.sh"]
      restartPolicy: Never
```

### Step 10.2: Create Post-Sync Hook

**post-sync-notification.yaml:**
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: post-sync-notification
  namespace: default
  annotations:
    argocd.argoproj.io/hook: PostSync
    argocd.argoproj.io/hook-delete-policy: HookSucceeded
spec:
  template:
    spec:
      containers:
      - name: notification
        image: curlimages/curl
        command: ["curl", "-X", "POST", "https://hooks.slack.com/..."]
      restartPolicy: Never
```

---

## Step 11: Configure RBAC

### Step 11.1: Create RBAC ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.csv: |
    p, role:admin, applications, *, */*, allow
    p, role:developer, applications, get, */*, allow
    p, role:developer, applications, sync, dev/*, allow
    p, role:developer, applications, sync, staging/*, allow
    g, developers, role:developer
```

Apply:
```bash
kubectl apply -f rbac-config.yaml
```

### Step 11.2: Create User Account

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: developer-account
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: account
stringData:
  password: <bcrypt-hashed-password>
  passwordMtime: "2024-01-15T10:00:00Z"
```

---

## Step 12: Monitor Applications

### Step 12.1: View Application Status

**Using CLI:**
```bash
# List all applications
argocd app list

# Get application details
argocd app get myapp

# Watch application
argocd app get myapp --watch
```

**Using Web UI:**
- View application tree
- Check resource health
- View sync status
- See application history

### Step 12.2: View Application Logs

```bash
# Application controller logs
kubectl logs -n argocd -l app.kubernetes.io/name=argocd-application-controller

# Repo server logs
kubectl logs -n argocd -l app.kubernetes.io/name=argocd-repo-server
```

---

## Step 13: Troubleshooting

### Application Not Syncing

1. **Check application status:**
   ```bash
   argocd app get myapp
   ```

2. **Check repository access:**
   ```bash
   argocd repo list
   ```

3. **Test repository connection:**
   ```bash
   argocd repo get https://github.com/yourusername/yourrepo
   ```

4. **Check application controller logs:**
   ```bash
   kubectl logs -n argocd -l app.kubernetes.io/name=argocd-application-controller
   ```

### Sync Failures

1. **View sync operation:**
   ```bash
   argocd app get myapp --refresh
   ```

2. **Check resource errors:**
   - Go to application in UI
   - Click on failed resources
   - View error messages

3. **Validate manifests:**
   ```bash
   argocd app diff myapp
   ```

### Repository Access Issues

1. **Add repository credentials:**
   ```bash
   argocd repo add https://github.com/yourusername/yourrepo \
     --username yourusername \
     --password yourpassword
   ```

2. **Add SSH repository:**
   ```bash
   argocd repo add git@github.com:yourusername/yourrepo.git \
     --ssh-private-key-path ~/.ssh/id_rsa
   ```

---

## Step 14: Clean Up

### Step 14.1: Delete Application

```bash
# Using CLI
argocd app delete myapp

# Using kubectl
kubectl delete application myapp -n argocd
```

### Step 14.2: Uninstall ArgoCD

```bash
# Delete all applications first
argocd app list -o name | xargs -n1 argocd app delete

# Delete ArgoCD
kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Delete namespace
kubectl delete namespace argocd
```

---

## Best Practices

1. **Use Projects:**
   - Group related applications
   - Apply policies at project level
   - Control access with RBAC

2. **Sync Policy:**
   - Use automatic sync for dev/staging
   - Use manual sync for production
   - Use sync windows for maintenance

3. **Health Checks:**
   - Define custom health checks
   - Monitor application health
   - Set up alerts

4. **Version Control:**
   - Tag releases in Git
   - Use semantic versioning
   - Pin chart versions

5. **Security:**
   - Use RBAC
   - Secure repository access
   - Use secrets for sensitive data

---

## Next Steps

- Set up multi-cluster ArgoCD
- Configure SSO/OIDC
- Implement sync windows
- Set up notifications
- Explore ApplicationSets
- Integrate with CI pipeline

---

## References

- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [ArgoCD Examples](https://github.com/argoproj/argocd-examples)
- [ArgoCD Best Practices](https://argo-cd.readthedocs.io/en/stable/user-guide/best_practices/)

---

**Happy GitOps! 🚀**

