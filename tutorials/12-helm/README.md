# Helm Tutorial

This tutorial will guide you through creating and using Helm charts for Kubernetes applications.

## Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to access your cluster
- Helm 3 installed (`helm version`)
- Basic understanding of Kubernetes concepts

## Objectives

By the end of this tutorial, you will:
1. Understand Helm concepts
2. Create a Helm chart from scratch
3. Customize charts with values
4. Install and manage Helm releases
5. Use Helm repositories
6. Understand Helm hooks and templates

---

## Step 1: Install Helm

### Verify Helm Installation

```bash
helm version
```

If Helm is not installed, install it:

**macOS:**
```bash
brew install helm
```

**Linux:**
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

---

## Step 2: Create Your First Helm Chart

### Step 2.1: Create Chart

```bash
helm create myapp
```

This creates a directory structure:
```
myapp/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── _helpers.tpl
│   └── NOTES.txt
└── charts/
```

### Step 2.2: Explore Chart Structure

**Chart.yaml:**
```yaml
apiVersion: v2
name: myapp
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: "1.0.0"
```

**values.yaml:**
Contains default configuration values.

**templates/:** Contains Kubernetes manifest templates.

### Step 2.3: Test Chart

```bash
# Lint the chart
helm lint ./myapp

# Dry run (see what would be installed)
helm install myapp ./myapp --dry-run --debug

# Template rendering (see generated YAML)
helm template myapp ./myapp
```

---

## Step 3: Customize the Chart

### Step 3.1: Update values.yaml

Edit `myapp/values.yaml`:

```yaml
replicaCount: 2

image:
  repository: nginx
  pullPolicy: IfNotPresent
  tag: "1.21"

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: true
  className: "nginx"
  annotations: {}
  hosts:
    - host: myapp.local
      paths:
        - path: /
          pathType: Prefix
  tls: []

resources:
  limits:
    cpu: 200m
    memory: 256Mi
  requests:
    cpu: 100m
    memory: 128Mi
```

### Step 3.2: Customize Deployment Template

Edit `myapp/templates/deployment.yaml` to see how values are used:

```yaml
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
      - name: {{ .Chart.Name }}
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        resources:
          {{- toYaml .Values.resources | nindent 10 }}
```

### Step 3.3: Test Customized Chart

```bash
helm template myapp ./myapp
```

---

## Step 4: Install the Chart

### Step 4.1: Install with Default Values

```bash
helm install myapp-release ./myapp
```

### Step 4.2: Check Installation

```bash
# List releases
helm list

# Get release status
helm status myapp-release

# Get release values
helm get values myapp-release

# Get release manifest
helm get manifest myapp-release
```

### Step 4.3: Verify in Kubernetes

```bash
kubectl get all -l app.kubernetes.io/instance=myapp-release
```

---

## Step 5: Customize with Values File

### Step 5.1: Create Custom Values File

Create `myapp/production-values.yaml`:

```yaml
replicaCount: 5

image:
  tag: "1.21-alpine"

service:
  type: LoadBalancer

resources:
  limits:
    cpu: 500m
    memory: 512Mi
  requests:
    cpu: 200m
    memory: 256Mi
```

### Step 5.2: Install with Custom Values

```bash
helm install production-app ./myapp -f ./myapp/production-values.yaml
```

### Step 5.3: Override Values on Command Line

```bash
helm install dev-app ./myapp \
  --set replicaCount=1 \
  --set image.tag=latest \
  --set service.type=NodePort
```

---

## Step 6: Upgrade and Rollback

### Step 6.1: Update Chart

Edit `myapp/Chart.yaml`:

```yaml
version: 0.2.0
```

Update `myapp/values.yaml`:

```yaml
replicaCount: 3
```

### Step 6.2: Upgrade Release

```bash
helm upgrade myapp-release ./myapp
```

### Step 6.3: Check Upgrade History

```bash
helm history myapp-release
```

### Step 6.4: Rollback

```bash
# Rollback to previous version
helm rollback myapp-release

# Rollback to specific revision
helm rollback myapp-release 1
```

---

## Step 7: Work with Helm Repositories

### Step 7.1: Add Repository

```bash
# Add Bitnami repository
helm repo add bitnami https://charts.bitnami.com/bitnami

# Add Prometheus repository
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

### Step 7.2: Update Repositories

```bash
helm repo update
```

### Step 7.3: Search Charts

```bash
# Search in added repositories
helm search repo nginx

# Search in Artifact Hub
helm search hub wordpress
```

### Step 7.4: Install from Repository

```bash
helm install my-nginx bitnami/nginx
```

---

## Step 8: Create a Custom Chart from Scratch

### Step 8.1: Create Chart Structure

```bash
mkdir -p mychart/templates
cd mychart
```

### Step 8.2: Create Chart.yaml

```yaml
apiVersion: v2
name: mychart
description: A simple Helm chart
type: application
version: 0.1.0
appVersion: "1.0.0"
```

### Step 8.3: Create values.yaml

```yaml
name: myapp
image: nginx:latest
replicas: 1
port: 80
```

### Step 8.4: Create Deployment Template

Create `templates/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Values.name }}
  labels:
    app: {{ .Values.name }}
spec:
  replicas: {{ .Values.replicas }}
  selector:
    matchLabels:
      app: {{ .Values.name }}
  template:
    metadata:
      labels:
        app: {{ .Values.name }}
    spec:
      containers:
      - name: {{ .Values.name }}
        image: {{ .Values.image }}
        ports:
        - containerPort: {{ .Values.port }}
```

### Step 8.5: Create Service Template

Create `templates/service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Values.name }}
spec:
  selector:
    app: {{ .Values.name }}
  ports:
  - port: {{ .Values.port }}
    targetPort: {{ .Values.port }}
  type: ClusterIP
```

### Step 8.6: Test and Install

```bash
# Lint
helm lint .

# Template
helm template mychart .

# Install
helm install myrelease .
```

---

## Step 9: Use Helm Hooks

### Step 9.1: Create Pre-Install Job

Create `templates/pre-install-job.yaml`:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: {{ .Release.Name }}-pre-install
  annotations:
    "helm.sh/hook": pre-install
    "helm.sh/hook-weight": "-5"
    "helm.sh/hook-delete-policy": before-hook-creation,hook-succeeded
spec:
  template:
    spec:
      restartPolicy: Never
      containers:
      - name: pre-install
        image: busybox
        command: ["/bin/sh", "-c", "echo 'Pre-install hook running' && sleep 5"]
```

### Step 9.2: Create Post-Install Job

Create `templates/post-install-job.yaml`:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: {{ .Release.Name }}-post-install
  annotations:
    "helm.sh/hook": post-install
    "helm.sh/hook-weight": "5"
    "helm.sh/hook-delete-policy": before-hook-creation,hook-succeeded
spec:
  template:
    spec:
      restartPolicy: Never
      containers:
      - name: post-install
        image: busybox
        command: ["/bin/sh", "-c", "echo 'Post-install hook running' && sleep 5"]
```

### Step 9.3: Test Hooks

```bash
helm install test-release ./mychart
# Watch for hook jobs
kubectl get jobs
```

---

## Step 10: Advanced Templates

### Step 10.1: Use Helper Templates

Create `templates/_helpers.tpl`:

```yaml
{{- define "mychart.fullname" -}}
{{- if .Values.fullnameOverride }}
{{- .Values.fullnameOverride | trunc 63 | trimSuffix "-" }}
{{- else }}
{{- $name := default .Chart.Name .Values.nameOverride }}
{{- printf "%s-%s" .Release.Name $name | trunc 63 | trimSuffix "-" }}
{{- end }}
{{- end }}

{{- define "mychart.labels" -}}
helm.sh/chart: {{ include "mychart.chart" . }}
{{ include "mychart.selectorLabels" . }}
{{- if .Chart.AppVersion }}
app.kubernetes.io/version: {{ .Chart.AppVersion | quote }}
{{- end }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{- end }}

{{- define "mychart.selectorLabels" -}}
app.kubernetes.io/name: {{ include "mychart.name" . }}
app.kubernetes.io/instance: {{ .Release.Name }}
{{- end }}
```

### Step 10.2: Use Helpers in Templates

Update `templates/deployment.yaml`:

```yaml
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    {{- include "mychart.labels" . | nindent 4 }}
```

### Step 10.3: Use Template Functions

```yaml
{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{ include "mychart.fullname" . }}
{{- end }}

{{- range .Values.env }}
- name: {{ .name }}
  value: {{ .value }}
{{- end }}

{{ .Values.name | upper }}
{{ .Values.count | int }}
```

---

## Step 11: Package and Share Charts

### Step 11.1: Package Chart

```bash
helm package ./mychart
# Creates mychart-0.1.0.tgz
```

### Step 11.2: Install from Package

```bash
helm install myrelease ./mychart-0.1.0.tgz
```

### Step 11.3: Create Chart Repository (Optional)

```bash
# Create index
helm repo index . --url https://charts.example.com

# Host files on web server or use ChartMuseum
```

---

## Step 12: Clean Up

### Step 12.1: Uninstall Releases

```bash
helm uninstall myapp-release
helm uninstall production-app
helm uninstall dev-app
```

### Step 12.2: Verify Cleanup

```bash
helm list
kubectl get all
```

---

## Troubleshooting

### Chart Not Installing

1. **Check lint errors:**
   ```bash
   helm lint ./mychart
   ```

2. **Dry run to see errors:**
   ```bash
   helm install myrelease ./mychart --dry-run --debug
   ```

3. **Check template rendering:**
   ```bash
   helm template myrelease ./mychart
   ```

### Values Not Working

1. **Check values file:**
   ```bash
   helm get values myrelease
   ```

2. **Verify value paths:**
   ```bash
   helm template myrelease ./mychart --debug
   ```

### Release Not Upgrading

1. **Check release status:**
   ```bash
   helm status myrelease
   ```

2. **Check history:**
   ```bash
   helm history myrelease
   ```

3. **Force upgrade:**
   ```bash
   helm upgrade myrelease ./mychart --force
   ```

---

## Best Practices

1. **Versioning:** Use semantic versioning for charts
2. **Values:** Provide sensible defaults
3. **Documentation:** Document all values
4. **Testing:** Always test with `--dry-run`
5. **Linting:** Run `helm lint` before packaging
6. **Helpers:** Use helper templates for reusability
7. **Security:** Don't hardcode secrets in charts

---

## Next Steps

- Explore Helm repositories
- Learn advanced templating
- Create chart dependencies
- Set up CI/CD with Helm
- Learn about Helm plugins

---

## References

- [Helm Documentation](https://helm.sh/docs/)
- [Helm Chart Best Practices](https://helm.sh/docs/chart_best_practices/)
- [Artifact Hub](https://artifacthub.io/)

---

**Happy Helming! 🚢**

