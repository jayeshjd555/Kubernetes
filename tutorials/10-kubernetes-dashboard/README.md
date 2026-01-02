# Kubernetes Dashboard Tutorial

This tutorial will guide you through installing and using the Kubernetes Dashboard, the official web-based UI for Kubernetes clusters.

## Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to access your cluster
- Basic understanding of Kubernetes concepts
- Admin access to the cluster (for installation)

## Objectives

By the end of this tutorial, you will:
1. Install Kubernetes Dashboard
2. Create a service account with proper permissions
3. Access the Dashboard UI
4. Understand how to use Dashboard features
5. Configure RBAC for Dashboard access

---

## Step 1: Install Kubernetes Dashboard

### Option 1: Using Official YAML (Recommended)

The easiest way to install Kubernetes Dashboard is using the official YAML manifest:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

This will create:
- `kubernetes-dashboard` namespace
- Dashboard deployment and service
- Service account and RBAC resources

### Option 2: Using Local YAML Files

Alternatively, you can use the provided YAML files:

```bash
kubectl apply -f dashboard-deployment.yml
```

### Verify Installation

Check that Dashboard pods are running:

```bash
kubectl get pods -n kubernetes-dashboard
```

You should see output like:
```
NAME                                         READY   STATUS    RESTARTS   AGE
kubernetes-dashboard-xxx                     1/1     Running   0          1m
dashboard-metrics-scraper-xxx                1/1     Running   0          1m
```

Check the Dashboard service:

```bash
kubectl get svc -n kubernetes-dashboard
```

---

## Step 2: Create Service Account

For accessing the Dashboard, we need to create a service account with appropriate permissions.

### Step 2.1: Create Service Account

Apply the service account:

```bash
kubectl apply -f serviceaccount.yml
```

### Step 2.2: Create ClusterRoleBinding

For this tutorial, we'll create an admin user. **Note:** In production, use more restrictive permissions.

Apply the ClusterRoleBinding:

```bash
kubectl apply -f clusterrolebinding.yml
```

**Security Note:** The `cluster-admin` role provides full cluster access. In production environments, create more restrictive roles based on your needs.

---

## Step 3: Get Access Token

### Step 3.1: Get Service Account Token

Retrieve the token for the service account:

```bash
kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode
```

Save this token - you'll need it to log in to the Dashboard.

### Alternative: Create Admin User (Optional)

If you want to create a dedicated admin user, you can use the provided `admin-user.yml`:

```bash
kubectl apply -f admin-user.yml
```

Then get the token:

```bash
kubectl -n kubernetes-dashboard get secret admin-user -o jsonpath="{.data.token}" | base64 --decode
```

---

## Step 4: Access Kubernetes Dashboard

### Option 1: Using kubectl proxy (Recommended for Local Access)

Start kubectl proxy:

```bash
kubectl proxy
```

This will start a proxy server on `http://localhost:8001`.

**Access Dashboard:**
Open your browser and navigate to:
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

### Option 2: Port Forwarding

Alternatively, you can use port forwarding:

```bash
kubectl port-forward -n kubernetes-dashboard svc/kubernetes-dashboard 8443:443
```

Then access:
```
https://localhost:8443
```

**Note:** You may need to accept the self-signed certificate warning.

### Option 3: NodePort (For Remote Access)

If you need to access Dashboard from outside the cluster, you can change the service type to NodePort:

```bash
kubectl patch svc kubernetes-dashboard -n kubernetes-dashboard -p '{"spec":{"type":"NodePort"}}'
```

Get the NodePort:

```bash
kubectl get svc -n kubernetes-dashboard kubernetes-dashboard
```

Access via: `https://<node-ip>:<nodeport>`

---

## Step 5: Log In to Dashboard

1. When you access the Dashboard URL, you'll see a login page
2. Select **"Token"** as the authentication method
3. Paste the token you retrieved in Step 3
4. Click **"Sign In"**

You should now see the Kubernetes Dashboard home page.

---

## Step 6: Explore Dashboard Features

### Overview

The Dashboard provides several views:

1. **Cluster Overview:**
   - Cluster resource usage
   - Node status
   - Namespace overview

2. **Workloads:**
   - Deployments
   - ReplicaSets
   - Pods
   - Jobs
   - CronJobs
   - StatefulSets
   - DaemonSets

3. **Services:**
   - Services
   - Ingresses

4. **Config & Storage:**
   - ConfigMaps
   - Secrets
   - PersistentVolumeClaims
   - Storage Classes

5. **Cluster:**
   - Nodes
   - Namespaces
   - PersistentVolumes
   - Roles
   - Cluster Roles

### Viewing Resources

1. **Navigate to a namespace:**
   - Click on "Namespaces" in the left menu
   - Select a namespace (e.g., "default")

2. **View Pods:**
   - Click on "Pods" in the left menu
   - See all pods in the selected namespace
   - Click on a pod to see details

3. **View Logs:**
   - Click on a pod
   - Click on the "Logs" tab
   - View real-time container logs

4. **Execute Commands:**
   - Click on a pod
   - Click on the "Exec" tab
   - Execute commands in the container

5. **View Events:**
   - Click on "Events" in the left menu
   - See recent events in the cluster

### Creating Resources

You can create resources directly from the Dashboard:

1. Click the **"+"** button in the top right
2. Choose to:
   - Create from file (YAML/JSON)
   - Create from form
   - Create from template

3. Fill in the required information
4. Click **"Create"**

### Editing Resources

1. Navigate to the resource you want to edit
2. Click on the resource name
3. Click the **"Edit"** button (pencil icon)
4. Modify the YAML
5. Click **"Update"**

### Deleting Resources

1. Navigate to the resource
2. Click the **"Delete"** button (trash icon)
3. Confirm the deletion

---

## Step 7: Create Sample Resources

Let's create some sample resources to explore Dashboard features:

### Create a Namespace

```bash
kubectl create namespace dashboard-demo
```

### Create a Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: dashboard-demo
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
        image: nginx:latest
        ports:
        - containerPort: 80
```

Save as `sample-deployment.yml` and apply:

```bash
kubectl apply -f sample-deployment.yml
```

### View in Dashboard

1. In Dashboard, select the `dashboard-demo` namespace
2. Navigate to "Workloads" → "Deployments"
3. You should see `nginx-deployment`
4. Click on it to see details
5. Click on "Pods" to see the pods created by the deployment
6. Click on a pod, then click "Logs" to view logs

---

## Step 8: Configure RBAC for Restricted Access

In production, you should create more restrictive RBAC instead of using cluster-admin.

### Example: Read-Only Access

Create a role with read-only permissions:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: dashboard-readonly
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["get", "list", "watch"]
- apiGroups: ["apps"]
  resources: ["*"]
  verbs: ["get", "list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: dashboard-readonly-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: dashboard-readonly
subjects:
- kind: ServiceAccount
  name: readonly-user
  namespace: kubernetes-dashboard
```

### Example: Namespace-Specific Access

Create a role for a specific namespace:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: namespace-admin
  namespace: dashboard-demo
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: namespace-admin-binding
  namespace: dashboard-demo
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: namespace-admin
subjects:
- kind: ServiceAccount
  name: namespace-admin-user
  namespace: kubernetes-dashboard
```

---

## Step 9: Troubleshooting

### Dashboard Not Accessible

1. **Check Dashboard pods:**
   ```bash
   kubectl get pods -n kubernetes-dashboard
   ```

2. **Check Dashboard service:**
   ```bash
   kubectl get svc -n kubernetes-dashboard
   ```

3. **Check Dashboard logs:**
   ```bash
   kubectl logs -n kubernetes-dashboard -l k8s-app=kubernetes-dashboard
   ```

### Token Not Working

1. **Verify service account exists:**
   ```bash
   kubectl get sa -n kubernetes-dashboard
   ```

2. **Check ClusterRoleBinding:**
   ```bash
   kubectl get clusterrolebinding admin-user
   ```

3. **Regenerate token:**
   ```bash
   kubectl delete secret -n kubernetes-dashboard admin-user-token
   # Token will be automatically recreated
   ```

### Certificate Issues

If you see certificate errors:

1. **For kubectl proxy:** This is normal, the proxy handles certificates
2. **For port-forward:** Accept the self-signed certificate in your browser
3. **For NodePort:** You may need to configure proper certificates

### Permission Denied Errors

If you see permission errors:

1. **Verify RBAC:**
   ```bash
   kubectl get clusterrolebinding admin-user -o yaml
   ```

2. **Check service account:**
   ```bash
   kubectl describe sa admin-user -n kubernetes-dashboard
   ```

3. **Test permissions:**
   ```bash
   kubectl auth can-i get pods --as=system:serviceaccount:kubernetes-dashboard:admin-user
   ```

---

## Step 10: Clean Up

To remove Kubernetes Dashboard:

```bash
kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

Or if using local files:

```bash
kubectl delete -f dashboard-deployment.yml
kubectl delete -f serviceaccount.yml
kubectl delete -f clusterrolebinding.yml
```

To remove the demo namespace:

```bash
kubectl delete namespace dashboard-demo
```

---

## Best Practices

1. **Security:**
   - Never expose Dashboard publicly without proper authentication
   - Use RBAC to restrict access based on user roles
   - Regularly rotate service account tokens
   - Use network policies to restrict Dashboard access

2. **Access Methods:**
   - Use `kubectl proxy` for local development
   - Use Ingress with TLS for production access
   - Consider using OIDC for authentication

3. **RBAC:**
   - Follow principle of least privilege
   - Create role-specific service accounts
   - Use namespaces to isolate access

4. **Monitoring:**
   - Monitor Dashboard resource usage
   - Set up alerts for Dashboard availability
   - Log Dashboard access for audit purposes

---

## Additional Resources

- [Kubernetes Dashboard GitHub](https://github.com/kubernetes/dashboard)
- [Kubernetes Dashboard Documentation](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)
- [RBAC Documentation](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)

---

**Happy Dashboarding! 🎛️**

