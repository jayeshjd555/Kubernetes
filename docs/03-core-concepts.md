
### Kubernetes Objects and its Management

Kubernetes objects are persistent entities in the Kubernetes system that represent the state of your cluster. They describe what containerized applications are running, what resources they're using, and policies around how those applications behave.

#### What are Kubernetes Objects?

**Kubernetes Objects** are records of intent - you create objects to describe the desired state of your cluster. Kubernetes continuously works to ensure the actual state matches your desired state.

**Key Characteristics:**
- **Persistent:** Objects persist in etcd (cluster state store)
- **Declarative:** You describe desired state, Kubernetes makes it happen
- **Managed:** Kubernetes controllers manage object lifecycle
- **Namespaced or Cluster-scoped:** Objects belong to namespaces or cluster-wide

#### Object Spec and Status

Every Kubernetes object has two important fields:

1. **spec:** Describes the desired state
   - What you want the object to look like
   - Defined by you when creating the object

2. **status:** Describes the actual state
   - Current state of the object
   - Managed by Kubernetes

**Example:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:                    # Desired state
  containers:
  - name: nginx
    image: nginx:latest
status:                  # Actual state (managed by Kubernetes)
  phase: Running
  conditions: [...]
```

#### Common Kubernetes Objects

**Workload Objects:**
- **Pod:** Smallest deployable unit (one or more containers)
- **Deployment:** Manages pod replicas and updates
- **ReplicaSet:** Ensures specified number of pod replicas
- **StatefulSet:** Manages stateful applications
- **DaemonSet:** Ensures pod runs on all/some nodes
- **Job:** Runs a task to completion
- **CronJob:** Runs jobs on a schedule

**Service Objects:**
- **Service:** Exposes pods as network service
- **Ingress:** Manages external HTTP/HTTPS access

**Configuration Objects:**
- **ConfigMap:** Stores configuration data
- **Secret:** Stores sensitive data
- **Namespace:** Logical grouping of resources

**Storage Objects:**
- **PersistentVolume:** Cluster-wide storage
- **PersistentVolumeClaim:** Request for storage
- **StorageClass:** Defines storage classes

**Policy Objects:**
- **ResourceQuota:** Limits resource usage
- **LimitRange:** Constraints on resources
- **NetworkPolicy:** Network access control

#### Object Management Methods

Kubernetes provides three ways to manage objects:

**1. Imperative Commands**
- Direct commands that immediately perform operations
- Good for learning and quick tasks
- Not recommended for production

```bash
# Create object
kubectl create deployment myapp --image=nginx

# Update object
kubectl scale deployment myapp --replicas=3

# Delete object
kubectl delete deployment myapp
```

**2. Imperative Object Configuration**
- Commands that specify the operation and configuration
- Configuration stored in files
- Operations are explicit

```bash
# Create from file
kubectl create -f deployment.yaml

# Replace object
kubectl replace -f deployment.yaml

# Delete object
kubectl delete -f deployment.yaml
```

**3. Declarative Object Configuration (Recommended)**
- You describe desired state in files
- Kubernetes determines operations needed
- Best for production and version control

```bash
# Apply configuration
kubectl apply -f deployment.yaml

# Kubernetes automatically:
# - Creates object if it doesn't exist
# - Updates object if it exists
# - Maintains desired state
```

#### Object YAML Structure

All Kubernetes objects follow a similar YAML structure:

```yaml
apiVersion: v1          # API version
kind: Pod              # Object type
metadata:              # Object metadata
  name: my-pod         # Object name
  namespace: default   # Namespace (optional)
  labels:              # Labels (optional)
    app: nginx
spec:                  # Desired state
  # Object-specific configuration
status:                # Actual state (read-only, managed by K8s)
  # Current state
```

**Required Fields:**
- `apiVersion`: Which version of Kubernetes API
- `kind`: What kind of object
- `metadata`: Object identification (name, namespace, labels)
- `spec`: Desired state (varies by object type)

#### Object Metadata

**Common Metadata Fields:**

```yaml
metadata:
  name: my-pod                    # Object name (required)
  namespace: default              # Namespace (optional)
  labels:                         # Key-value pairs for selection
    app: nginx
    env: production
  annotations:                    # Non-identifying metadata
    description: "Web server pod"
  uid: "123e4567-e89b-12d3..."   # Unique ID (auto-generated)
  resourceVersion: "12345"        # Version for optimistic concurrency
  generation: 1                   # Generation number
  creationTimestamp: "2024-01-01T00:00:00Z"
```

**Labels:**
- Key-value pairs attached to objects
- Used for selection and organization
- Example: `app=nginx`, `env=production`

**Annotations:**
- Key-value pairs for metadata
- Not used for selection
- Example: `description`, `contact-info`

#### Object Lifecycle

**Lifecycle Stages:**

1. **Creation:** Object created via API
2. **Validation:** Kubernetes validates the object
3. **Storage:** Object stored in etcd
4. **Reconciliation:** Controllers reconcile desired vs actual state
5. **Deletion:** Object marked for deletion
6. **Finalization:** Finalizers run (if any)
7. **Removal:** Object removed from etcd

#### Managing Objects with kubectl

**Viewing Objects:**

```bash
# List objects
kubectl get <object-type>
kubectl get pods
kubectl get deployments

# Get specific object
kubectl get <object-type> <object-name>
kubectl get pod my-pod

# Describe object (detailed info)
kubectl describe <object-type> <object-name>
kubectl describe pod my-pod

# Get object YAML
kubectl get <object-type> <object-name> -o yaml

# Get object JSON
kubectl get <object-type> <object-name> -o json
```

**Creating Objects:**

```bash
# From YAML file
kubectl apply -f object.yaml
kubectl create -f object.yaml

# From stdin
cat object.yaml | kubectl apply -f -

# Multiple files
kubectl apply -f file1.yaml -f file2.yaml

# Directory
kubectl apply -f ./manifests/
```

**Updating Objects:**

```bash
# Apply changes (declarative)
kubectl apply -f updated-object.yaml

# Edit object interactively
kubectl edit <object-type> <object-name>

# Patch object
kubectl patch <object-type> <object-name> -p '{"spec":{"replicas":3}}'

# Replace object
kubectl replace -f object.yaml
```

**Deleting Objects:**

```bash
# Delete by name
kubectl delete <object-type> <object-name>

# Delete from file
kubectl delete -f object.yaml

# Delete by label
kubectl delete <object-type> -l app=nginx

# Delete all objects of type
kubectl delete <object-type> --all
```

#### Object Relationships

Objects in Kubernetes often relate to each other:

**Owner References:**
- Objects can have owners
- Deleting owner deletes owned objects (cascading deletion)
- Example: ReplicaSet owns Pods

**Labels and Selectors:**
- Objects use labels to identify related objects
- Selectors match labels
- Example: Service selects Pods by labels

**Dependencies:**
- Some objects depend on others
- Example: Pod depends on ConfigMap/Secret

#### Best Practices for Object Management

**1. Use Declarative Configuration**
```bash
# ✅ Good: Declarative
kubectl apply -f deployment.yaml

# ⚠️ Avoid: Imperative
kubectl create deployment myapp --image=nginx
```

**2. Version Control Your Configurations**
- Store YAML files in Git
- Track changes over time
- Enable rollback capability

**3. Use Meaningful Names**
```yaml
# ✅ Good
name: frontend-deployment

# ⚠️ Avoid
name: app1
```

**4. Use Labels Consistently**
```yaml
labels:
  app: frontend
  env: production
  team: web
```

**5. Organize with Namespaces**
- Group related objects in namespaces
- Separate environments
- Apply policies per namespace

**6. Use kubectl apply**
- Idempotent operations
- Handles creation and updates
- Better for automation

#### Common kubectl Commands

```bash
# Get objects
kubectl get <resource>
kubectl get pods
kubectl get deployments
kubectl get services

# Describe object
kubectl describe <resource> <name>

# Create/Apply
kubectl apply -f <file>
kubectl create -f <file>

# Edit
kubectl edit <resource> <name>

# Delete
kubectl delete <resource> <name>
kubectl delete -f <file>

# Watch
kubectl get <resource> -w

# Output formats
kubectl get <resource> -o yaml
kubectl get <resource> -o json
kubectl get <resource> -o wide
```

#### Key Takeaways

1. **Objects are persistent entities** - Represent cluster state
2. **Declarative approach** - Describe desired state, Kubernetes makes it happen
3. **spec vs status** - spec is desired, status is actual
4. **Use kubectl apply** - Recommended for object management
5. **Version control** - Store YAML files in Git
6. **Labels and selectors** - Organize and select objects
7. **Namespaces** - Organize objects logically
8. **Object relationships** - Objects relate through owners and selectors

Understanding Kubernetes objects and their management is fundamental to working with Kubernetes effectively.

---

### Field Selectors

**Field Selectors** allow you to filter Kubernetes objects based on field values. They provide a way to select objects using specific field values rather than labels.

#### What are Field Selectors?

Field selectors let you select Kubernetes objects based on the values of one or more resource fields. Unlike label selectors, field selectors filter by actual field values in the object.

**Key Characteristics:**
- Filter objects by field values
- Different from label selectors
- Support different fields per resource type
- Used with `kubectl get` commands

#### Field Selector Syntax

```bash
# Basic syntax
kubectl get <resource> --field-selector <field>=<value>

# Multiple selectors (AND logic)
kubectl get <resource> --field-selector <field1>=<value1>,<field2>=<value2>
```

#### Common Field Selectors

**For Pods:**

```bash
# Select pods by node
kubectl get pods --field-selector spec.nodeName=node-1

# Select pods by phase (Running, Pending, Succeeded, Failed)
kubectl get pods --field-selector status.phase=Running

# Select pods not assigned to any node
kubectl get pods --field-selector spec.nodeName=

# Multiple selectors
kubectl get pods --field-selector status.phase=Running,spec.nodeName=node-1
```

**For Nodes:**

```bash
# Select nodes by condition
kubectl get nodes --field-selector status.conditions[?(@.type=="Ready")].status=True

# Select nodes by unschedulable status
kubectl get nodes --field-selector spec.unschedulable=false
```

**For PersistentVolumes:**

```bash
# Select by phase
kubectl get pv --field-selector status.phase=Available

# Select by storage class
kubectl get pv --field-selector spec.storageClassName=fast-ssd
```

#### Field Selector vs Label Selector

| Aspect | Field Selector | Label Selector |
|--------|---------------|----------------|
| **Syntax** | `--field-selector` | `-l` or `--selector` |
| **Filters by** | Object field values | Label key-value pairs |
| **Use Case** | System fields (status, spec) | Custom organization |
| **Examples** | `status.phase=Running` | `app=nginx` |
| **Flexibility** | Limited to specific fields | Any custom labels |

**When to Use Field Selectors:**
- Filter by system fields (status, phase, node assignment)
- Find objects in specific states
- Filter by infrastructure-related fields

**When to Use Label Selectors:**
- Filter by application/team/environment
- Custom organization and grouping
- Application-specific filtering

#### Practical Examples

**Example 1: Find Running Pods**

```bash
# Get all running pods
kubectl get pods --field-selector status.phase=Running

# Get running pods in specific namespace
kubectl get pods -n production --field-selector status.phase=Running
```

**Example 2: Find Pods on Specific Node**

```bash
# Get all pods on node-1
kubectl get pods --field-selector spec.nodeName=node-1

# Get pods on node-1 that are running
kubectl get pods --field-selector spec.nodeName=node-1,status.phase=Running
```

**Example 3: Find Unassigned Pods**

```bash
# Pods not yet assigned to a node
kubectl get pods --field-selector spec.nodeName=

# Useful for debugging scheduling issues
```

**Example 4: Find Available PersistentVolumes**

```bash
# Get available persistent volumes
kubectl get pv --field-selector status.phase=Available

# Get bound persistent volumes
kubectl get pv --field-selector status.phase=Bound
```

**Example 5: Combine with Other Filters**

```bash
# Running pods in production namespace on node-1
kubectl get pods -n production \
  --field-selector status.phase=Running,spec.nodeName=node-1

# With label selector
kubectl get pods -l app=nginx \
  --field-selector status.phase=Running
```

#### Available Field Selectors by Resource

**Pods:**
- `spec.nodeName` - Node name
- `spec.restartPolicy` - Restart policy
- `status.phase` - Pod phase (Pending, Running, Succeeded, Failed)
- `status.podIP` - Pod IP address
- `metadata.namespace` - Namespace

**Nodes:**
- `metadata.name` - Node name
- `spec.unschedulable` - Schedulable status

**PersistentVolumes:**
- `status.phase` - Volume phase (Available, Bound, Released, Failed)
- `spec.storageClassName` - Storage class name

**PersistentVolumeClaims:**
- `status.phase` - Claim phase
- `spec.storageClassName` - Storage class name

#### Field Selector Limitations

**Not All Fields are Supported:**
- Only specific fields can be used in field selectors
- Complex fields (arrays, nested objects) may not be supported
- Field selector support varies by resource type

**Comparison Operators:**
- Field selectors support equality (`=`) only
- No support for `!=`, `>`, `<`, `in`, `notin` like label selectors
- For complex filtering, use label selectors or `jq`/`grep`

#### Combining Field and Label Selectors

You can combine both selectors for powerful filtering:

```bash
# Pods with label app=nginx that are running
kubectl get pods -l app=nginx --field-selector status.phase=Running

# Pods with label env=production on node-1
kubectl get pods -l env=production --field-selector spec.nodeName=node-1
```

#### Field Selector Best Practices

1. **Use for System Fields**
   - Status, phase, node assignment
   - Infrastructure-related filtering

2. **Combine with Labels**
   - Use field selectors for system state
   - Use label selectors for application logic

3. **Know the Limitations**
   - Not all fields are supported
   - Only equality comparison

4. **Use for Debugging**
   - Find pods in specific states
   - Debug scheduling issues
   - Monitor resource status

#### Field Selector Commands Summary

```bash
# Basic field selector
kubectl get <resource> --field-selector <field>=<value>

# Multiple field selectors
kubectl get <resource> --field-selector <field1>=<value1>,<field2>=<value2>

# Combine with label selector
kubectl get <resource> -l <label> --field-selector <field>=<value>

# Common pod field selectors
kubectl get pods --field-selector status.phase=Running
kubectl get pods --field-selector spec.nodeName=node-1
kubectl get pods --field-selector spec.nodeName=

# Common PV field selectors
kubectl get pv --field-selector status.phase=Available
```

#### Key Takeaways

1. **Field selectors filter by field values** - Not labels
2. **Use for system fields** - Status, phase, node assignment
3. **Limited to equality** - Only `=` operator supported
4. **Combine with labels** - Use both for powerful filtering
5. **Not all fields supported** - Check documentation for available fields
6. **Useful for debugging** - Find objects in specific states
7. **Different from label selectors** - Each has its use case

Field selectors are a powerful tool for filtering Kubernetes objects based on their actual field values, especially useful for system-level filtering and debugging.

---

### Namespaces

**Namespaces** in Kubernetes provide a way to divide cluster resources between multiple users, teams, or projects. Think of namespaces as virtual clusters within a physical Kubernetes cluster.

#### What are Namespaces?

Namespaces are a mechanism for organizing and isolating resources in a Kubernetes cluster. They provide:

- **Resource Isolation:** Resources in different namespaces are isolated from each other
- **Resource Quotas:** Set resource limits per namespace
- **Access Control:** Apply RBAC policies per namespace
- **Organization:** Group related resources together

#### Default Namespaces

Kubernetes comes with several built-in namespaces:

1. **default**
   - Default namespace for resources when no namespace is specified
   - Where your resources go if you don't specify a namespace

2. **kube-system**
   - System namespace for Kubernetes system components
   - Contains system pods (kube-proxy, kube-dns, etc.)
   - ⚠️ **Don't create user resources here**

3. **kube-public**
   - Publicly accessible namespace
   - Contains cluster information readable by all users
   - Rarely used

4. **kube-node-lease**
   - Used for node heartbeat
   - Helps detect node failures
   - Managed by Kubernetes automatically

#### Why Use Namespaces?

**1. Resource Organization**
```
Production Environment
├── production namespace
│   ├── frontend pods
│   ├── backend pods
│   └── database pods
└── staging namespace
    ├── frontend pods
    ├── backend pods
    └── database pods
```

**2. Resource Quotas**
- Limit CPU and memory per namespace
- Prevent one team from consuming all resources
- Enforce resource limits per project

**3. Access Control**
- Different teams can have access to different namespaces
- Isolate sensitive environments (production)
- Apply different security policies

**4. Environment Separation**
- Separate development, staging, and production
- Avoid conflicts between environments
- Easy to clean up (delete namespace)

#### Creating Namespaces

**Method 1: Using kubectl**

```bash
# Create namespace
kubectl create namespace <namespace-name>

# Example
kubectl create namespace development
kubectl create namespace production
```

**Method 2: Using YAML**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: development
```

```bash
# Apply YAML
kubectl apply -f namespace.yaml
```

**Example Reference:**
- **[nginx/namespace.yml](../nginx/namespace.yml)** - Example namespace YAML file for nginx namespace

**Method 3: Using kubectl with YAML (one-liner)**

```bash
kubectl create namespace development --dry-run=client -o yaml | kubectl apply -f -
```

#### Viewing Namespaces

```bash
# List all namespaces
kubectl get namespaces
# or
kubectl get ns

# View specific namespace
kubectl get namespace <namespace-name>
# or
kubectl get ns <namespace-name>

# Describe namespace
kubectl describe namespace <namespace-name>
```

#### Working with Namespaces

**Creating Resources in a Namespace**

```bash
# Method 1: Use --namespace or -n flag
kubectl create deployment myapp --image=nginx -n development

# Method 2: Set default namespace context
kubectl config set-context --current --namespace=development
kubectl create deployment myapp --image=nginx

# Method 3: Specify in YAML
```

**YAML Example:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: development  # Specify namespace here
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

**Viewing Resources in Namespaces**

```bash
# List pods in specific namespace
kubectl get pods -n development

# List all resources in namespace
kubectl get all -n development

# List resources across all namespaces
kubectl get pods --all-namespaces
# or
kubectl get pods -A
```

**Switching Namespace Context**

```bash
# Set default namespace for current context
kubectl config set-context --current --namespace=development

# Verify current namespace
kubectl config view --minify | grep namespace

# List current namespace
kubectl config view --minify -o jsonpath='{..namespace}'
```

#### Deleting Namespaces

```bash
# Delete namespace (deletes all resources in it)
kubectl delete namespace <namespace-name>

# Example
kubectl delete namespace development

# ⚠️ Warning: This deletes ALL resources in the namespace!
```

**Important Notes:**
- ⚠️ Deleting a namespace deletes **all resources** in that namespace
- ⚠️ Default and kube-system namespaces **cannot be deleted**
- ⚠️ Namespace deletion is asynchronous (may take time)

#### Namespace Best Practices

**1. Use Namespaces for Environments**
```
development namespace → Development environment
staging namespace → Staging environment
production namespace → Production environment
```

**2. Use Namespaces for Teams**
```
team-a namespace → Team A's resources
team-b namespace → Team B's resources
```

**3. Use Namespaces for Applications**
```
app-frontend namespace → Frontend application
app-backend namespace → Backend application
```

**4. Set Resource Quotas**
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: development
spec:
  hard:
    requests.cpu: "4"
    requests.memory: 8Gi
    limits.cpu: "8"
    limits.memory: 16Gi
```

**5. Apply RBAC Policies**
- Use Role and RoleBinding for namespace-level permissions
- Grant access only to necessary namespaces

#### Common Namespace Patterns

**Pattern 1: Environment-Based**
```
production
staging
development
testing
```

**Pattern 2: Team-Based**
```
team-frontend
team-backend
team-devops
```

**Pattern 3: Application-Based**
```
app-user-service
app-order-service
app-payment-service
```

**Pattern 4: Hybrid**
```
production-frontend
production-backend
staging-frontend
staging-backend
```

#### Namespace Limitations

**What Namespaces DON'T Provide:**
- ❌ **Network Isolation:** Pods in different namespaces can still communicate
- ❌ **Complete Security:** Not a security boundary by itself
- ❌ **Resource Isolation:** Resources are still in the same cluster

**What You Need for True Isolation:**
- Network Policies (for network isolation)
- RBAC (for access control)
- Resource Quotas (for resource limits)

#### Namespace Commands Summary

```bash
# Create
kubectl create namespace <name>

# List
kubectl get namespaces
kubectl get ns

# Describe
kubectl describe namespace <name>

# Delete
kubectl delete namespace <name>

# Set default
kubectl config set-context --current --namespace=<name>

# View resources in namespace
kubectl get <resource> -n <namespace>

# View resources in all namespaces
kubectl get <resource> --all-namespaces
kubectl get <resource> -A
```

#### Practical Examples

**Example 1: Create Development Environment**

```bash
# Create namespace
kubectl create namespace development

# Create deployment in development namespace
kubectl create deployment myapp --image=nginx -n development

# View pods in development
kubectl get pods -n development
```

**Example 2: Switch Between Namespaces**

```bash
# Work in development
kubectl config set-context --current --namespace=development
kubectl get pods

# Switch to production
kubectl config set-context --current --namespace=production
kubectl get pods
```

**Example 3: Compare Resources Across Namespaces**

```bash
# View all pods across namespaces
kubectl get pods --all-namespaces

# View deployments in specific namespaces
kubectl get deployments -n development
kubectl get deployments -n production
```

#### Example Reference

For a practical example of a namespace YAML file, check out:

- **[nginx/namespace.yml](../nginx/namespace.yml)** - Example namespace definition for creating an nginx namespace

This example demonstrates:
- Basic namespace YAML structure
- How to define a namespace with metadata
- Simple namespace creation pattern

#### Key Takeaways

1. **Namespaces organize resources** - Group related resources together
2. **Namespaces provide isolation** - Separate environments, teams, or applications
3. **Use --namespace or -n flag** - Specify namespace in commands
4. **Set default namespace** - Use `kubectl config set-context` for convenience
5. **Namespaces don't provide network isolation** - Use Network Policies for that
6. **Delete namespace carefully** - It deletes all resources in it
7. **Use Resource Quotas** - Limit resources per namespace
8. **Apply RBAC** - Control access per namespace

Namespaces are a fundamental concept in Kubernetes that help you organize and manage resources effectively in your cluster.

---

### Pods

**Pods** are the smallest deployable units in Kubernetes. A Pod represents a single instance of a running process in your cluster and can contain one or more containers.

#### What is a Pod?

A Pod is a group of one or more containers that:
- Share the same network namespace (same IP address)
- Share the same storage volumes
- Are scheduled together on the same node
- Have the same lifecycle (created, started, stopped together)

**Key Characteristics:**
- **Smallest unit:** Cannot split a Pod across nodes
- **Ephemeral:** Pods are created and destroyed, not updated
- **One IP per Pod:** All containers in a Pod share the same IP
- **Localhost communication:** Containers in same Pod can communicate via localhost

#### Pod Structure

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: default
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

#### Single Container Pods

Most common use case - one container per Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

#### Multi-Container Pods

Pods can contain multiple containers that work together:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
  - name: sidecar
    image: busybox:latest
    command: ['sh', '-c', 'while true; do echo hello; sleep 10; done']
```

**Use Cases for Multi-Container Pods:**
- **Sidecar pattern:** Helper container (logging, monitoring)
- **Adapter pattern:** Transform data format
- **Ambassador pattern:** Proxy network requests

#### Pod Lifecycle

**Pod Phases:**

1. **Pending:** Pod accepted by cluster, but containers not created
2. **Running:** Pod bound to node, all containers created, at least one running
3. **Succeeded:** All containers terminated successfully
4. **Failed:** At least one container terminated with failure
5. **Unknown:** Pod state cannot be determined

**Pod States:**

```bash
# View pod phases
kubectl get pods

# Output shows:
# NAME        READY   STATUS    RESTARTS   AGE
# my-pod      1/1     Running   0          5m
```

#### Creating Pods

**Method 1: Using kubectl**

```bash
# Create pod from YAML
kubectl create -f pod.yaml

# Create pod imperatively
kubectl run nginx-pod --image=nginx:latest

# Create pod with namespace
kubectl create -f pod.yaml -n my-namespace
```

**Method 2: Using YAML (Recommended)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

```bash
kubectl apply -f pod.yaml
```

**Method 3: Using kubectl run**

```bash
# Basic pod
kubectl run my-pod --image=nginx:latest

# With labels
kubectl run my-pod --image=nginx:latest --labels="app=nginx,env=prod"

# With namespace
kubectl run my-pod --image=nginx:latest -n my-namespace
```

#### Viewing Pods

```bash
# List all pods
kubectl get pods

# List pods in namespace
kubectl get pods -n my-namespace

# List pods across all namespaces
kubectl get pods --all-namespaces
kubectl get pods -A

# Get pod details
kubectl get pod <pod-name>

# Describe pod (detailed information)
kubectl describe pod <pod-name>

# Get pod YAML
kubectl get pod <pod-name> -o yaml

# Get pod JSON
kubectl get pod <pod-name> -o json

# Watch pods
kubectl get pods -w
```

#### Pod Logs

```bash
# View pod logs
kubectl logs <pod-name>

# View logs from specific container (multi-container pod)
kubectl logs <pod-name> -c <container-name>

# Follow logs (like tail -f)
kubectl logs -f <pod-name>

# View logs from previous container instance (if crashed)
kubectl logs <pod-name> --previous

# View last N lines
kubectl logs <pod-name> --tail=100

# View logs since timestamp
kubectl logs <pod-name> --since=10m
```

#### Executing Commands in Pods

```bash
# Execute command in pod
kubectl exec <pod-name> -- <command>

# Example
kubectl exec nginx-pod -- ls /usr/share/nginx/html

# Interactive shell
kubectl exec -it <pod-name> -- /bin/bash

# Execute in specific container (multi-container pod)
kubectl exec -it <pod-name> -c <container-name> -- /bin/bash
```

#### Pod Configuration

**Container Image:**

```yaml
spec:
  containers:
  - name: nginx
    image: nginx:latest          # Use specific tag
    imagePullPolicy: Always     # Always, IfNotPresent, Never
```

**Container Ports:**

```yaml
spec:
  containers:
  - name: nginx
    ports:
    - containerPort: 80
      protocol: TCP
      name: http
    - containerPort: 443
      protocol: TCP
      name: https
```

**Environment Variables:**

```yaml
spec:
  containers:
  - name: nginx
    env:
    - name: ENV_VAR
      value: "value"
    - name: DB_HOST
      valueFrom:
        configMapKeyRef:
          name: my-config
          key: db-host
    - name: SECRET_KEY
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: password
```

**Resource Limits:**

```yaml
spec:
  containers:
  - name: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

**Volume Mounts:**

```yaml
spec:
  containers:
  - name: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/nginx
  volumes:
  - name: config-volume
    configMap:
      name: nginx-config
```

#### Pod Restart Policy

```yaml
spec:
  restartPolicy: Always    # Always, OnFailure, Never
  containers:
  - name: nginx
    image: nginx:latest
```

**Restart Policies:**
- **Always:** Always restart container (default for Pods)
- **OnFailure:** Restart only on failure
- **Never:** Never restart container

#### Pod Health Checks

**Liveness Probe:**

```yaml
spec:
  containers:
  - name: nginx
    livenessProbe:
      httpGet:
        path: /health
        port: 80
      initialDelaySeconds: 30
      periodSeconds: 10
      timeoutSeconds: 5
      failureThreshold: 3
```

**Readiness Probe:**

```yaml
spec:
  containers:
  - name: nginx
    readinessProbe:
      httpGet:
        path: /ready
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
```

**Startup Probe:**

```yaml
spec:
  containers:
  - name: nginx
    startupProbe:
      httpGet:
        path: /startup
        port: 80
      failureThreshold: 30
      periodSeconds: 10
```

**Probe Types:**
- **httpGet:** HTTP GET request
- **tcpSocket:** TCP connection check
- **exec:** Execute command

#### Pod Status and Conditions

**View Pod Status:**

```bash
# Get pod status
kubectl get pod <pod-name> -o jsonpath='{.status.phase}'

# Get pod conditions
kubectl get pod <pod-name> -o jsonpath='{.status.conditions}'

# Get pod IP
kubectl get pod <pod-name> -o jsonpath='{.status.podIP}'

# Get node name
kubectl get pod <pod-name> -o jsonpath='{.spec.nodeName}'
```

**Pod Conditions:**
- **PodScheduled:** Pod assigned to node
- **Initialized:** Init containers completed
- **ContainersReady:** All containers ready
- **Ready:** Pod ready to serve traffic

#### Pod Networking

**Pod IP:**
- Each Pod gets its own IP address
- Containers in same Pod share the same IP
- Pods can communicate using Pod IPs

**Port Forwarding:**

```bash
# Forward local port to pod
kubectl port-forward <pod-name> 8080:80

# Access pod on localhost:8080
curl http://localhost:8080
```

**Pod-to-Pod Communication:**
- Pods can communicate using Pod IPs
- Use Service for stable networking
- Network policies control access

#### Pod Storage

**Ephemeral Storage:**
- Pods have ephemeral storage
- Data lost when Pod is deleted
- Use volumes for persistence

**Volume Types:**

```yaml
spec:
  containers:
  - name: nginx
    volumeMounts:
    - name: data
      mountPath: /data
  volumes:
  - name: data
    emptyDir: {}              # Temporary storage
  # - name: data
  #   persistentVolumeClaim:
  #     claimName: my-pvc     # Persistent storage
```

#### Pod Security

**Security Context:**

```yaml
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
  - name: nginx
    securityContext:
      allowPrivilegeEscalation: false
      capabilities:
        drop:
        - ALL
        add:
        - NET_BIND_SERVICE
      readOnlyRootFilesystem: true
```

**Service Account:**

```yaml
spec:
  serviceAccountName: my-service-account
  containers:
  - name: nginx
    image: nginx:latest
```

#### Pod Scheduling

**Node Selection:**

```yaml
spec:
  nodeSelector:
    disktype: ssd
    zone: us-east-1a
```

**Node Affinity:**

```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: zone
            operator: In
            values:
            - us-east-1a
```

**Pod Affinity:**

```yaml
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - nginx
        topologyKey: kubernetes.io/hostname
```

**Taints and Tolerations:**

```yaml
spec:
  tolerations:
  - key: "key1"
    operator: "Equal"
    value: "value1"
    effect: "NoSchedule"
```

#### Init Containers

Init containers run before the main containers:

```yaml
spec:
  initContainers:
  - name: init-db
    image: busybox
    command: ['sh', '-c', 'until nslookup db-service; do echo waiting; sleep 2; done']
  containers:
  - name: nginx
    image: nginx:latest
```

**Use Cases:**
- Wait for dependencies
- Initialize data
- Run setup scripts

#### Pod Deletion

```bash
# Delete pod
kubectl delete pod <pod-name>

# Delete pod by file
kubectl delete -f pod.yaml

# Force delete (immediate)
kubectl delete pod <pod-name> --force --grace-period=0

# Delete all pods in namespace
kubectl delete pods --all -n my-namespace
```

**Graceful Termination:**
- Pod receives SIGTERM signal
- Grace period (default 30 seconds)
- Then SIGKILL if not terminated

#### Pod Best Practices

**1. Don't Create Pods Directly**
- Use Deployments, StatefulSets, or DaemonSets
- Pods are ephemeral and not self-healing

**2. Use Specific Image Tags**
```yaml
# ✅ Good
image: nginx:1.21.6

# ⚠️ Avoid
image: nginx:latest
```

**3. Set Resource Limits**
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

**4. Use Health Checks**
```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 80
readinessProbe:
  httpGet:
    path: /ready
    port: 80
```

**5. Use Labels**
```yaml
metadata:
  labels:
    app: nginx
    env: production
    version: v1.0
```

**6. Use Namespaces**
```yaml
metadata:
  namespace: production
```

**7. Set Security Context**
```yaml
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
```

#### Common Pod Issues and Troubleshooting

**Pod Pending:**
```bash
# Check why pod is pending
kubectl describe pod <pod-name>

# Common causes:
# - Insufficient resources
# - Node selector/affinity not matching
# - Taints without tolerations
```

**Pod CrashLoopBackOff:**
```bash
# Check pod logs
kubectl logs <pod-name>

# Check previous container logs
kubectl logs <pod-name> --previous

# Describe pod for events
kubectl describe pod <pod-name>
```

**Pod Not Ready:**
```bash
# Check readiness probe
kubectl describe pod <pod-name>

# Check container status
kubectl get pod <pod-name> -o jsonpath='{.status.containerStatuses}'
```

**Image Pull Errors:**
```bash
# Check image pull policy
kubectl get pod <pod-name> -o jsonpath='{.spec.containers[0].imagePullPolicy}'

# Common issues:
# - Image doesn't exist
# - Image pull secrets missing
# - Network issues
```

#### Advanced Pod Features

**Pod Disruption Budget:**

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: nginx
```

**Pod Priority:**

```yaml
apiVersion: v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000
---
spec:
  priorityClassName: high-priority
```

**Pod Preset (Deprecated):**
- Pod Presets are deprecated
- Use Init Containers or Admission Controllers instead

#### Pod Commands Summary

```bash
# Create
kubectl create -f pod.yaml
kubectl run <name> --image=<image>

# Get
kubectl get pods
kubectl get pod <name>
kubectl describe pod <name>

# Logs
kubectl logs <pod-name>
kubectl logs -f <pod-name>

# Exec
kubectl exec -it <pod-name> -- /bin/bash

# Port forward
kubectl port-forward <pod-name> 8080:80

# Delete
kubectl delete pod <name>
kubectl delete -f pod.yaml

# Edit
kubectl edit pod <name>
```

#### Example Reference

For a practical example of a Pod YAML file, check out:

- **[nginx/pod.yml](../nginx/pod.yml)** - Example Pod definition for nginx

This example demonstrates:
- Basic Pod structure with metadata
- Container specification with image
- Port configuration
- Namespace assignment
- Simple single-container Pod pattern

**To use this example:**
```bash
# Apply the pod
kubectl apply -f nginx/pod.yml

# View the pod
kubectl get pods -n nginx

# Check pod status
kubectl describe pod nginx-pod -n nginx

# View logs
kubectl logs nginx-pod -n nginx

# Access pod
kubectl exec -it nginx-pod -n nginx -- /bin/bash
```

#### Key Takeaways

1. **Pods are the smallest deployable unit** - Cannot split across nodes
2. **Pods are ephemeral** - Created and destroyed, not updated
3. **Containers in Pod share network and storage** - Same IP, shared volumes
4. **Don't create Pods directly** - Use Deployments, StatefulSets, or DaemonSets
5. **Set resource limits** - Prevent resource exhaustion
6. **Use health checks** - Liveness and readiness probes
7. **Pods get their own IP** - All containers in Pod share the IP
8. **Use labels** - For organization and selection
9. **Pods are not self-healing** - Use controllers for reliability
10. **One container per Pod is common** - Multi-container Pods for specific use cases

Pods are the fundamental building blocks of Kubernetes applications. Understanding Pods is essential for working with Kubernetes effectively.

---

### Labels and Selectors

**Labels** and **Selectors** are fundamental concepts in Kubernetes that enable you to organize and select groups of objects. They are key-value pairs attached to objects for identification and selection.

#### What are Labels?

**Labels** are key-value pairs attached to Kubernetes objects (Pods, Services, Deployments, etc.) that are used to:
- **Organize objects** - Group related objects
- **Identify objects** - Mark objects with meaningful attributes
- **Select objects** - Find and operate on groups of objects
- **Filter objects** - Query and filter based on labels

**Key Characteristics:**
- **Key-Value Pairs:** Simple string key-value pairs
- **Attached to Objects:** Part of object metadata
- **Not Unique:** Multiple objects can have same labels
- **Queryable:** Can be used to select objects

#### Label Syntax

**Label Format:**
- **Key:** Alphanumeric, hyphens, underscores, dots
- **Value:** Alphanumeric, hyphens, underscores, dots
- **Length:** Max 63 characters each
- **Prefix:** Optional prefix (e.g., `kubernetes.io/`, `app.kubernetes.io/`)

**Valid Examples:**
```yaml
labels:
  app: nginx
  env: production
  version: "1.0"
  tier: frontend
  app.kubernetes.io/name: nginx
  app.kubernetes.io/version: "1.21"
```

**Invalid Examples:**
```yaml
labels:
  app/name: nginx        # ❌ Slash not allowed in key (unless prefix)
  app name: nginx        # ❌ Space not allowed
  app: nginx:latest      # ❌ Colon not allowed in value
```

#### Adding Labels to Objects

**Method 1: In YAML**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
    env: production
    version: "1.0"
    tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

**Method 2: Using kubectl**

```bash
# Add label to existing object
kubectl label pod nginx-pod env=production

# Add multiple labels
kubectl label pod nginx-pod env=production tier=frontend

# Overwrite existing label
kubectl label pod nginx-pod env=staging --overwrite

# Remove label
kubectl label pod nginx-pod env-
```

#### Viewing Labels

```bash
# List objects with labels
kubectl get pods --show-labels

# Show specific labels
kubectl get pods -L app,env

# Get label value
kubectl get pod nginx-pod -o jsonpath='{.metadata.labels.app}'
```

#### Common Label Conventions

**Recommended Labels (by Kubernetes):**

```yaml
labels:
  app.kubernetes.io/name: nginx
  app.kubernetes.io/instance: nginx-prod
  app.kubernetes.io/version: "1.21"
  app.kubernetes.io/component: web
  app.kubernetes.io/part-of: myapp
  app.kubernetes.io/managed-by: kubectl
```

**Common Custom Labels:**

```yaml
labels:
  app: nginx
  env: production
  version: "1.0"
  tier: frontend
  team: web
  region: us-east-1
```

#### What are Selectors?

**Selectors** are expressions used to select objects based on their labels. They allow you to:
- **Find objects** - Query objects by labels
- **Group objects** - Operate on groups of objects
- **Link objects** - Connect related objects (e.g., Service to Pods)

**Types of Selectors:**
1. **Equality-based** - Match exact label values
2. **Set-based** - Match using operators (In, NotIn, Exists, DoesNotExist)

#### Label Selectors

**Equality-Based Selectors:**

```yaml
selector:
  matchLabels:
    app: nginx
    env: production
```

**Set-Based Selectors:**

```yaml
selector:
  matchExpressions:
  - key: app
    operator: In
    values:
    - nginx
    - apache
  - key: env
    operator: NotIn
    values:
    - test
    - dev
  - key: tier
    operator: Exists
  - key: version
    operator: DoesNotExist
```

**Operators:**
- **In:** Value in list
- **NotIn:** Value not in list
- **Exists:** Key exists (no values needed)
- **DoesNotExist:** Key doesn't exist (no values needed)

#### Using Selectors with kubectl

**Equality-Based:**

```bash
# Select pods with label app=nginx
kubectl get pods -l app=nginx

# Select pods with multiple labels
kubectl get pods -l app=nginx,env=production

# Select pods without label
kubectl get pods -l '!env'

# Select pods with env not equal to test
kubectl get pods -l 'env!=test'
```

**Set-Based:**

```bash
# Select pods where app is nginx or apache
kubectl get pods -l 'app in (nginx,apache)'

# Select pods where env is not test or dev
kubectl get pods -l 'env notin (test,dev)'

# Select pods where tier exists
kubectl get pods -l 'tier'

# Select pods where version doesn't exist
kubectl get pods -l '!version'
```

#### Selectors in Kubernetes Objects

**ReplicaSet Selector:**

```yaml
apiVersion: apps/v1
kind: ReplicaSet
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx  # Must match selector
```

**Service Selector:**

```yaml
apiVersion: v1
kind: Service
spec:
  selector:
    app: nginx
    tier: frontend
```

**Deployment Selector:**

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  selector:
    matchLabels:
      app: nginx
    matchExpressions:
    - key: env
      operator: In
      values:
      - production
      - staging
```

#### Label Selector Examples

**Example 1: Simple Selection**

```yaml
# Pod with labels
metadata:
  labels:
    app: nginx
    env: production

# Service selecting the pod
spec:
  selector:
    app: nginx
    env: production
```

**Example 2: Complex Selection**

```yaml
# Deployment with set-based selector
spec:
  selector:
    matchLabels:
      app: nginx
    matchExpressions:
    - key: env
      operator: In
      values:
      - production
      - staging
    - key: version
      operator: Exists
```

#### Label Best Practices

**1. Use Standard Labels**

```yaml
labels:
  app.kubernetes.io/name: nginx
  app.kubernetes.io/version: "1.21"
```

**2. Use Consistent Naming**

```yaml
# ✅ Good - Consistent
labels:
  app: nginx
  env: production

# ⚠️ Avoid - Inconsistent
labels:
  application: nginx
  environment: production
```

**3. Use Meaningful Labels**

```yaml
# ✅ Good
labels:
  app: frontend
  tier: web
  env: production

# ⚠️ Avoid
labels:
  a: f
  t: w
  e: p
```

**4. Don't Over-Label**

```yaml
# ✅ Good - Essential labels
labels:
  app: nginx
  env: production

# ⚠️ Avoid - Too many labels
labels:
  app: nginx
  env: production
  team: web
  region: us-east-1
  zone: us-east-1a
  instance: nginx-1
  # ... many more
```

**5. Use Labels for Organization**

```yaml
# Organize by application
labels:
  app: nginx

# Organize by environment
labels:
  env: production

# Organize by team
labels:
  team: frontend
```

#### Label Selector Matching

**Important Rules:**

1. **Selector must match labels** - Objects selected must have matching labels
2. **Template labels must match selector** - In ReplicaSet/Deployment, pod template labels must match selector
3. **Service selector matches pod labels** - Service selects pods by matching labels

**Example - ReplicaSet:**

```yaml
spec:
  selector:
    matchLabels:
      app: nginx        # Selector
  template:
    metadata:
      labels:
        app: nginx      # Must match selector
```

#### Label Selector Commands

```bash
# Get objects by label
kubectl get pods -l app=nginx
kubectl get pods -l app=nginx,env=production

# Get objects without label
kubectl get pods -l '!env'

# Get objects with label not equal
kubectl get pods -l 'env!=test'

# Get objects with set-based selector
kubectl get pods -l 'app in (nginx,apache)'
kubectl get pods -l 'env notin (test,dev)'
kubectl get pods -l 'tier'
kubectl get pods -l '!version'

# Add label
kubectl label pod nginx-pod env=production

# Remove label
kubectl label pod nginx-pod env-

# Update label
kubectl label pod nginx-pod env=staging --overwrite
```

#### Label Selector Use Cases

**1. Service to Pod Selection**

```yaml
# Service
spec:
  selector:
    app: nginx

# Pods (selected by service)
metadata:
  labels:
    app: nginx
```

**2. Deployment to Pod Selection**

```yaml
# Deployment
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
```

**3. Filtering and Querying**

```bash
# Get all production pods
kubectl get pods -l env=production

# Get all frontend pods
kubectl get pods -l tier=frontend

# Get pods in production or staging
kubectl get pods -l 'env in (production,staging)'
```

#### Key Takeaways

1. **Labels are key-value pairs** - Attached to objects for organization
2. **Selectors match labels** - Used to find and select objects
3. **Equality and set-based** - Two types of selectors
4. **Template labels must match selector** - In ReplicaSet/Deployment
5. **Use standard labels** - Follow Kubernetes conventions
6. **Be consistent** - Use consistent label names across objects
7. **Labels enable selection** - Services, Deployments use selectors
8. **Labels are queryable** - Use with kubectl -l flag

Labels and selectors are fundamental to how Kubernetes organizes and connects objects. Understanding them is essential for working with Kubernetes effectively.

---

### Annotations

**Annotations** are key-value pairs that can be attached to Kubernetes objects to store arbitrary, non-identifying metadata. Unlike labels, annotations are not used for selection or querying.

#### What are Annotations?

**Annotations** are metadata attached to Kubernetes objects that:
- **Store metadata** - Additional information about objects
- **Not for selection** - Unlike labels, not used for selection
- **Arbitrary data** - Can store any string data
- **Tool integration** - Used by tools and extensions

**Key Characteristics:**
- **Key-Value Pairs:** Similar to labels but for metadata
- **Not Queryable:** Cannot use annotations in selectors
- **Arbitrary Data:** Can store any string (including JSON)
- **Tool Usage:** Used by tools, libraries, and extensions

#### Annotations vs Labels

| Aspect | Labels | Annotations |
|--------|--------|-------------|
| **Purpose** | Identification and selection | Metadata storage |
| **Queryable** | ✅ Yes (with selectors) | ❌ No |
| **Used for Selection** | ✅ Yes | ❌ No |
| **Data Type** | String values | String values (can be JSON) |
| **Length** | Max 63 chars | No limit |
| **Use Case** | Organization, selection | Metadata, tool integration |

#### Annotation Syntax

**Annotation Format:**
- **Key:** Similar to labels (alphanumeric, hyphens, dots)
- **Value:** Any string (can include JSON, URLs, etc.)
- **No Length Limit:** Unlike labels
- **Prefix:** Optional prefix (e.g., `kubernetes.io/`)

**Valid Examples:**
```yaml
annotations:
  description: "Web server pod"
  contact: "team@example.com"
  build-version: "1.2.3"
  deployment.kubernetes.io/revision: "1"
  kubectl.kubernetes.io/last-applied-configuration: '{"apiVersion":"v1"...}'
```

#### Adding Annotations

**Method 1: In YAML**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  annotations:
    description: "Web server pod for production"
    contact: "web-team@example.com"
    build-version: "1.2.3"
    deployment-date: "2024-01-15"
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

**Method 2: Using kubectl**

```bash
# Add annotation
kubectl annotate pod nginx-pod description="Web server pod"

# Add multiple annotations
kubectl annotate pod nginx-pod \
  description="Web server pod" \
  contact="team@example.com"

# Overwrite existing annotation
kubectl annotate pod nginx-pod description="Updated description" --overwrite

# Remove annotation
kubectl annotate pod nginx-pod description-
```

#### Viewing Annotations

```bash
# View annotations
kubectl get pod nginx-pod -o jsonpath='{.metadata.annotations}'

# View specific annotation
kubectl get pod nginx-pod -o jsonpath='{.metadata.annotations.description}'

# View with describe
kubectl describe pod nginx-pod

# View in YAML
kubectl get pod nginx-pod -o yaml
```

#### Common Annotations

**Kubernetes System Annotations:**

```yaml
annotations:
  # Deployment revision
  deployment.kubernetes.io/revision: "2"
  
  # Last applied configuration
  kubectl.kubernetes.io/last-applied-configuration: '{"apiVersion":"v1"...}'
  
  # Pod disruption budget
  pod-disruption-budget.kubernetes.io/name: "my-pdb"
```

**Custom Annotations:**

```yaml
annotations:
  # Build information
  build-version: "1.2.3"
  build-date: "2024-01-15"
  git-commit: "abc1234"
  
  # Contact information
  contact: "team@example.com"
  owner: "web-team"
  
  # Documentation
  description: "Web server pod"
  documentation: "https://docs.example.com/nginx"
  
  # Tool integration
  prometheus.io/scrape: "true"
  prometheus.io/port: "8080"
```

#### Annotation Use Cases

**1. Build and Version Information**

```yaml
annotations:
  build-version: "1.2.3"
  build-date: "2024-01-15T10:00:00Z"
  git-commit: "abc1234"
  git-branch: "main"
```

**2. Contact and Ownership**

```yaml
annotations:
  contact: "team@example.com"
  owner: "web-team"
  slack-channel: "#web-team"
```

**3. Tool Integration**

```yaml
annotations:
  # Prometheus scraping
  prometheus.io/scrape: "true"
  prometheus.io/port: "8080"
  prometheus.io/path: "/metrics"
  
  # Linkerd
  linkerd.io/inject: enabled
  
  # Istio
  sidecar.istio.io/inject: "true"
```

**4. Documentation**

```yaml
annotations:
  description: "Web server pod for production environment"
  documentation: "https://docs.example.com/nginx"
  runbook: "https://runbook.example.com/nginx"
```

**5. Deployment Information**

```yaml
annotations:
  deployment-date: "2024-01-15"
  deployed-by: "ci-cd-pipeline"
  deployment-reason: "Rolling update"
```

**6. JSON Data**

```yaml
annotations:
  config: '{"database":"mysql","port":3306}'
  metadata: '{"team":"web","environment":"production"}'
```

#### Annotations in Different Objects

**Pod Annotations:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    description: "Web server pod"
    build-version: "1.2.3"
```

**Deployment Annotations:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "2"
    description: "Web server deployment"
```

**Service Annotations:**

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"
    description: "Load balancer service"
```

#### Annotation Best Practices

**1. Use for Metadata, Not Selection**

```yaml
# ✅ Good - Use annotations for metadata
annotations:
  description: "Web server pod"
  contact: "team@example.com"

# ❌ Wrong - Use labels for selection
annotations:
  app: nginx  # Should be in labels
```

**2. Use Standard Prefixes**

```yaml
annotations:
  # Kubernetes standard
  deployment.kubernetes.io/revision: "2"
  
  # Tool-specific
  prometheus.io/scrape: "true"
  
  # Custom with prefix
  mycompany.com/team: "web"
```

**3. Store Structured Data as JSON**

```yaml
annotations:
  config: '{"database":"mysql","port":3306}'
  metadata: '{"team":"web","env":"prod"}'
```

**4. Document Important Information**

```yaml
annotations:
  description: "Web server pod"
  documentation: "https://docs.example.com"
  runbook: "https://runbook.example.com"
```

**5. Don't Store Sensitive Data**

```yaml
# ❌ Bad - Don't store secrets
annotations:
  password: "secret123"  # Use Secrets instead

# ✅ Good - Store non-sensitive metadata
annotations:
  description: "Web server pod"
```

#### Annotations Commands Summary

```bash
# Add annotation
kubectl annotate <resource> <name> <key>=<value>

# Add multiple annotations
kubectl annotate <resource> <name> <key1>=<value1> <key2>=<value2>

# Overwrite annotation
kubectl annotate <resource> <name> <key>=<value> --overwrite

# Remove annotation
kubectl annotate <resource> <name> <key>-

# View annotations
kubectl get <resource> <name> -o jsonpath='{.metadata.annotations}'

# View specific annotation
kubectl get <resource> <name> -o jsonpath='{.metadata.annotations.<key>}'
```

#### Practical Examples

**Example 1: Add Build Information**

```bash
# Add build annotations to pod
kubectl annotate pod nginx-pod \
  build-version="1.2.3" \
  build-date="2024-01-15" \
  git-commit="abc1234"
```

**Example 2: Add Tool Integration**

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    prometheus.io/scrape: "true"
    prometheus.io/port: "8080"
    prometheus.io/path: "/metrics"
```

**Example 3: Add Documentation**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    description: "Web server deployment"
    documentation: "https://docs.example.com/nginx"
    contact: "web-team@example.com"
```

#### Key Takeaways

1. **Annotations store metadata** - Not used for selection
2. **Not queryable** - Cannot use in selectors
3. **Arbitrary data** - Can store any string (including JSON)
4. **No length limit** - Unlike labels (63 chars)
5. **Tool integration** - Used by monitoring, service mesh, etc.
6. **Use for metadata** - Description, contact, build info
7. **Don't store secrets** - Use Secrets for sensitive data
8. **Use standard prefixes** - For tool integration

Annotations complement labels by providing a way to store additional metadata that doesn't need to be queryable or used for selection.

---

### ReplicaSet

**ReplicaSet** ensures that a specified number of pod replicas are running at any given time. It's a lower-level concept that is typically managed by Deployments.

#### What is a ReplicaSet?

A ReplicaSet is a Kubernetes object that:
- Maintains a stable set of pod replicas
- Ensures desired number of pods are running
- Replaces pods that fail or are deleted
- Uses label selectors to identify pods

**Key Characteristics:**
- **Replica Management:** Maintains desired number of replicas
- **Self-Healing:** Automatically replaces failed pods
- **Label-Based:** Uses label selectors to manage pods
- **Lower-Level:** Usually managed by Deployments

#### ReplicaSet Diagram

