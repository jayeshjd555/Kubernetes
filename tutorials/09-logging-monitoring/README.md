# Logging and Monitoring Tutorial

This tutorial will guide you through setting up logging and monitoring in Kubernetes using Fluentd for log collection and Prometheus with Grafana for monitoring.

## Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to access your cluster
- Basic understanding of Kubernetes concepts

## Objectives

By the end of this tutorial, you will:
1. Understand how Kubernetes logging works
2. Set up Fluentd as a DaemonSet for log collection
3. Deploy Prometheus for metrics collection
4. Deploy Grafana for visualization
5. Create dashboards and queries

---

## Step 1: Create Namespace

First, let's create a dedicated namespace for our logging and monitoring stack:

```bash
kubectl apply -f namespace.yml
```

Verify the namespace was created:

```bash
kubectl get namespace logging-monitoring
```

---

## Step 2: Deploy Sample Application

Let's deploy a sample application that generates logs:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-app
  namespace: logging-monitoring
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sample-app
  template:
    metadata:
      labels:
        app: sample-app
    spec:
      containers:
      - name: app
        image: busybox
        command: ["sh", "-c"]
        args:
          - |
            while true; do
              echo "$(date) [INFO] Application is running"
              echo "$(date) [ERROR] Simulated error message" >&2
              sleep 5
            done
```

Save this as `sample-app.yml` and apply:

```bash
kubectl apply -f sample-app.yml
```

Check the logs:

```bash
kubectl logs -n logging-monitoring -l app=sample-app --tail=10
```

---

## Step 3: Set Up Fluentd for Log Collection

Fluentd will collect logs from all pods in the cluster.

### Step 3.1: Create Fluentd ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: fluentd-config
  namespace: logging-monitoring
data:
  fluent.conf: |
    <source>
      @type tail
      path /var/log/pods/**/*.log
      pos_file /var/log/fluentd-containers.log.pos
      tag kubernetes.*
      read_from_head true
      <parse>
        @type json
        time_key time
        time_format %Y-%m-%dT%H:%M:%S.%NZ
        keep_time_key true
      </parse>
    </source>
    
    <filter kubernetes.**>
      @type kubernetes_metadata
    </filter>
    
    <match kubernetes.**>
      @type stdout
    </match>
```

Save this as `fluentd-configmap.yml` and apply:

```bash
kubectl apply -f fluentd-configmap.yml
```

### Step 3.2: Deploy Fluentd DaemonSet

Apply the Fluentd DaemonSet:

```bash
kubectl apply -f fluentd-daemonset.yml
```

Verify Fluentd pods are running:

```bash
kubectl get pods -n logging-monitoring -l app=fluentd
```

Check Fluentd logs:

```bash
kubectl logs -n logging-monitoring -l app=fluentd --tail=20
```

You should see logs from your sample application being collected.

---

## Step 4: Deploy Prometheus

Prometheus will collect metrics from the cluster.

### Step 4.1: Create Prometheus ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
  namespace: logging-monitoring
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
      evaluation_interval: 15s
    
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      
      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
          - role: pod
        relabel_configs:
          - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
            action: keep
            regex: true
          - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
            action: replace
            target_label: __metrics_path__
            regex: (.+)
          - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
            action: replace
            regex: ([^:]+)(?::\d+)?;(\d+)
            replacement: $1:$2
            target_label: __address__
```

Save this as `prometheus-configmap.yml` and apply:

```bash
kubectl apply -f prometheus-configmap.yml
```

### Step 4.2: Deploy Prometheus

Apply the Prometheus deployment:

```bash
kubectl apply -f prometheus-deployment.yml
```

Verify Prometheus is running:

```bash
kubectl get pods -n logging-monitoring -l app=prometheus
```

### Step 4.3: Access Prometheus UI

Port-forward to access Prometheus:

```bash
kubectl port-forward -n logging-monitoring svc/prometheus 9090:9090
```

Open `http://localhost:9090` in your browser.

Try some queries:
- `up` - Shows which targets are up
- `container_cpu_usage_seconds_total` - CPU usage
- `container_memory_usage_bytes` - Memory usage

---

## Step 5: Deploy Grafana

Grafana will provide visualization for both logs and metrics.

### Step 5.1: Deploy Grafana

Apply the Grafana deployment:

```bash
kubectl apply -f grafana-deployment.yml
```

Verify Grafana is running:

```bash
kubectl get pods -n logging-monitoring -l app=grafana
```

### Step 5.2: Access Grafana UI

Port-forward to access Grafana:

```bash
kubectl port-forward -n logging-monitoring svc/grafana 3000:3000
```

Open `http://localhost:3000` in your browser.

**Default credentials:**
- Username: `admin`
- Password: `admin` (you'll be prompted to change it)

### Step 5.3: Add Prometheus Data Source

1. Go to **Configuration** → **Data Sources**
2. Click **Add data source**
3. Select **Prometheus**
4. Enter URL: `http://prometheus:9090`
5. Click **Save & Test**

### Step 5.4: Create a Dashboard

1. Go to **Dashboards** → **New Dashboard**
2. Click **Add visualization**
3. Select **Prometheus** as data source
4. Enter query: `rate(container_cpu_usage_seconds_total[5m])`
5. Click **Run query**
6. Save the dashboard

---

## Step 6: View Logs in Grafana

### Step 6.1: Add Loki Data Source (Optional)

If you want to view logs in Grafana, you can set up Loki:

1. Go to **Configuration** → **Data Sources**
2. Click **Add data source**
3. Select **Loki**
4. Enter URL: `http://loki:3100` (if Loki is deployed)
5. Click **Save & Test**

### Step 6.2: Query Logs

Use LogQL to query logs:

```logql
{namespace="logging-monitoring", app="sample-app"}
```

---

## Step 7: Create Alerts

### Step 7.1: Configure AlertManager (Optional)

If you want to set up alerting:

1. Deploy AlertManager
2. Configure alert rules in Prometheus
3. Set up notification channels (email, Slack, etc.)

### Step 7.2: Example Alert Rule

```yaml
groups:
  - name: example
    rules:
      - alert: HighCPUUsage
        expr: rate(container_cpu_usage_seconds_total[5m]) > 0.8
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High CPU usage detected"
          description: "CPU usage is above 80%"
```

---

## Step 8: Clean Up

To remove all resources:

```bash
kubectl delete namespace logging-monitoring
```

---

## Troubleshooting

### Fluentd Not Collecting Logs

1. Check Fluentd pod logs:
   ```bash
   kubectl logs -n logging-monitoring -l app=fluentd
   ```

2. Verify Fluentd has access to log files:
   ```bash
   kubectl exec -n logging-monitoring -l app=fluentd -- ls /var/log/pods
   ```

3. Check ConfigMap is mounted correctly:
   ```bash
   kubectl describe pod -n logging-monitoring -l app=fluentd
   ```

### Prometheus Not Scraping Metrics

1. Check Prometheus targets:
   - Go to Prometheus UI → Status → Targets
   - Verify targets are up

2. Check Prometheus logs:
   ```bash
   kubectl logs -n logging-monitoring -l app=prometheus
   ```

3. Verify service discovery:
   ```bash
   kubectl get pods --show-labels
   ```

### Grafana Not Connecting to Prometheus

1. Verify Prometheus service:
   ```bash
   kubectl get svc -n logging-monitoring prometheus
   ```

2. Test connectivity from Grafana pod:
   ```bash
   kubectl exec -n logging-monitoring -l app=grafana -- wget -O- http://prometheus:9090/api/v1/query?query=up
   ```

3. Check data source configuration in Grafana UI

---

## Best Practices

1. **Resource Limits:** Always set resource requests and limits for monitoring components
2. **Storage:** Use PersistentVolumes for Prometheus and Grafana data
3. **Retention:** Configure appropriate retention policies for metrics and logs
4. **Security:** Use RBAC to restrict access to monitoring data
5. **Scaling:** Monitor the monitoring stack itself to ensure it's not overloaded

---

## Next Steps

- Set up log aggregation with Elasticsearch or Loki
- Configure alerting rules for critical metrics
- Create custom dashboards for your applications
- Implement distributed tracing
- Set up APM (Application Performance Monitoring)

---

## References

- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)
- [Fluentd Documentation](https://docs.fluentd.org/)
- [Kubernetes Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)

---

**Happy Monitoring! 📊**

