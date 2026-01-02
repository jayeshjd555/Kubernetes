# Service Mesh Tutorial - Istio

This tutorial will guide you through setting up and using Istio service mesh in Kubernetes.

## Prerequisites

- A running Kubernetes cluster (v1.19+)
- `kubectl` configured to access your cluster
- Admin access to the cluster
- At least 2 CPU and 4GB RAM available
- Basic understanding of Kubernetes concepts

## Objectives

By the end of this tutorial, you will:
1. Install Istio service mesh
2. Enable automatic sidecar injection
3. Deploy applications with Istio
4. Configure traffic management
5. Enable mTLS security
6. Set up observability tools
7. Use Istio features (retries, timeouts, circuit breakers)

---

## Step 1: Install Istio

### Step 1.1: Download Istio

```bash
# Download latest Istio
curl -L https://istio.io/downloadIstio | sh -

# Navigate to Istio directory
cd istio-*

# Add Istio to PATH
export PATH=$PWD/bin:$PATH
```

### Step 1.2: Verify Installation

```bash
istioctl version
```

### Step 1.3: Pre-Installation Check

```bash
istioctl verify-install
```

### Step 1.4: Install Istio

**Option 1: Default Installation**
```bash
istioctl install
```

**Option 2: Demo Profile (for learning)**
```bash
istioctl install --set profile=demo
```

**Option 3: Minimal Profile (for production)**
```bash
istioctl install --set profile=minimal
```

### Step 1.5: Verify Installation

```bash
# Check control plane pods
kubectl get pods -n istio-system

# Check services
kubectl get svc -n istio-system

# Verify Istio version
istioctl version
```

You should see pods like:
- `istiod-xxx`
- `istio-ingressgateway-xxx` (if using demo profile)
- `istio-egressgateway-xxx` (if using demo profile)

---

## Step 2: Enable Automatic Sidecar Injection

### Step 2.1: Label Namespace

```bash
# Label default namespace
kubectl label namespace default istio-injection=enabled

# Verify
kubectl get namespace -L istio-injection
```

### Step 2.2: Create Test Namespace

```bash
# Create a new namespace
kubectl create namespace test-mesh

# Label it for injection
kubectl label namespace test-mesh istio-injection=enabled
```

---

## Step 3: Deploy Sample Applications

### Step 3.1: Deploy Bookinfo Application

The Bookinfo application is Istio's sample application:

```bash
# Deploy Bookinfo
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/platform/kube/bookinfo.yaml

# Wait for pods to be ready
kubectl wait --for=condition=ready pod -l app=ratings --timeout=300s
kubectl wait --for=condition=ready pod -l app=reviews --timeout=300s
kubectl wait --for=condition=ready pod -l app=productpage --timeout=300s
```

### Step 3.2: Verify Deployment

```bash
# Check pods (should have 2 containers each - app + sidecar)
kubectl get pods

# Check that sidecars are injected
kubectl get pods -o jsonpath='{.items[*].spec.containers[*].name}'
```

You should see `istio-proxy` containers alongside application containers.

### Step 3.3: Expose Application

```bash
# Create Gateway
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/networking/bookinfo-gateway.yaml

# Get ingress gateway IP
kubectl get svc istio-ingressgateway -n istio-system
```

### Step 3.4: Access Application

```bash
# Port-forward to ingress gateway
kubectl port-forward -n istio-system svc/istio-ingressgateway 8080:80

# Access application
curl http://localhost:8080/productpage
```

Or set environment variable:
```bash
export GATEWAY_URL=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

# If using NodePort or port-forward
export GATEWAY_URL=localhost:8080

# Access
curl http://$GATEWAY_URL/productpage
```

---

## Step 4: Configure Traffic Management

### Step 4.1: Create VirtualService

Create `virtualservice-reviews.yaml`:

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
    - match:
        - headers:
            end-user:
              exact: jason
      route:
        - destination:
            host: reviews
            subset: v2
    - route:
        - destination:
            host: reviews
            subset: v1
          weight: 50
        - destination:
            host: reviews
            subset: v2
          weight: 50
```

Apply:
```bash
kubectl apply -f virtualservice-reviews.yaml
```

### Step 4.2: Create DestinationRule

Create `destinationrule-reviews.yaml`:

```yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: reviews
spec:
  host: reviews
  subsets:
    - name: v1
      labels:
        version: v1
    - name: v2
      labels:
        version: v2
    - name: v3
      labels:
        version: v3
```

Apply:
```bash
kubectl apply -f destinationrule-reviews.yaml
```

### Step 4.3: Test Traffic Routing

```bash
# Access as regular user (should see v1 and v2)
for i in {1..10}; do curl -s http://$GATEWAY_URL/productpage | grep -o "color=\"black\"" | head -1; done

# Access as jason (should see v2)
for i in {1..10}; do curl -s -H "end-user: jason" http://$GATEWAY_URL/productpage | grep -o "color=\"red\"" | head -1; done
```

---

## Step 5: Enable mTLS Security

### Step 5.1: Create PeerAuthentication

Create `peerauthentication.yaml`:

```yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: default
spec:
  mtls:
    mode: STRICT
```

Apply:
```bash
kubectl apply -f peerauthentication.yaml
```

### Step 5.2: Verify mTLS

```bash
# Check mTLS status
istioctl authn tls-check $(kubectl get pod -l app=productpage -o jsonpath='{.items[0].metadata.name}') productpage.default.svc.cluster.local
```

### Step 5.3: Test Application

```bash
# Application should still work
curl http://$GATEWAY_URL/productpage
```

---

## Step 6: Set Up Observability

### Step 6.1: Install Add-ons

```bash
# Install Prometheus, Grafana, Jaeger, Kiali
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/addons/prometheus.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/addons/grafana.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/addons/jaeger.yaml
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/addons/kiali.yaml
```

### Step 6.2: Access Dashboards

**Grafana:**
```bash
istioctl dashboard grafana
# Access: http://localhost:3000
```

**Prometheus:**
```bash
istioctl dashboard prometheus
# Access: http://localhost:9090
```

**Jaeger:**
```bash
istioctl dashboard jaeger
# Access: http://localhost:16686
```

**Kiali:**
```bash
istioctl dashboard kiali
# Access: http://localhost:20001
# Default credentials: admin/admin
```

### Step 6.3: Generate Traffic

```bash
# Generate some traffic
for i in {1..100}; do curl -s http://$GATEWAY_URL/productpage > /dev/null; done
```

### Step 6.4: View Metrics

**In Kiali:**
1. Open Kiali dashboard
2. Go to "Graph" view
3. Select "default" namespace
4. View service topology and metrics

**In Grafana:**
1. Open Grafana dashboard
2. Go to "Istio" dashboards
3. View service metrics

---

## Step 7: Configure Retries and Timeouts

### Step 7.1: Create VirtualService with Retries

Create `virtualservice-retries.yaml`:

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
    - ratings
  http:
    - route:
        - destination:
            host: ratings
            subset: v1
      retries:
        attempts: 3
        perTryTimeout: 2s
      timeout: 10s
```

Apply:
```bash
kubectl apply -f virtualservice-retries.yaml
```

### Step 7.2: Test Retries

```bash
# Generate traffic and observe retries in metrics
for i in {1..50}; do curl -s http://$GATEWAY_URL/productpage > /dev/null; done

# Check metrics in Prometheus or Kiali
```

---

## Step 8: Configure Circuit Breaker

### Step 8.1: Create DestinationRule with Circuit Breaker

Create `destinationrule-circuitbreaker.yaml`:

```yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: ratings
spec:
  host: ratings
  subsets:
    - name: v1
      labels:
        version: v1
      trafficPolicy:
        connectionPool:
          tcp:
            maxConnections: 1
          http:
            http1MaxPendingRequests: 1
            maxRequestsPerConnection: 1
        circuitBreaker:
          consecutiveErrors: 1
          interval: 1s
          baseEjectionTime: 3m
          maxEjectionPercent: 100
```

Apply:
```bash
kubectl apply -f destinationrule-circuitbreaker.yaml
```

### Step 8.2: Test Circuit Breaker

```bash
# Generate traffic to trigger circuit breaker
for i in {1..10}; do curl -s http://$GATEWAY_URL/productpage; done
```

---

## Step 9: Implement Canary Deployment

### Step 9.1: Deploy New Version

```bash
# Deploy v2 of productpage
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/platform/kube/bookinfo.yaml
```

### Step 9.2: Create VirtualService for Canary

Create `virtualservice-canary.yaml`:

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: productpage
spec:
  hosts:
    - productpage
  http:
    - route:
        - destination:
            host: productpage
            subset: v1
          weight: 90
        - destination:
            host: productpage
            subset: v2
          weight: 10
```

Apply:
```bash
kubectl apply -f virtualservice-canary.yaml
```

### Step 9.3: Gradually Increase Traffic

```yaml
# Update to 50/50
weight: 50  # for both v1 and v2

# Then 100% to v2
weight: 0   # for v1
weight: 100 # for v2
```

---

## Step 10: Clean Up

### Step 10.1: Remove Applications

```bash
# Remove Bookinfo
kubectl delete -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/platform/kube/bookinfo.yaml
kubectl delete -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/networking/bookinfo-gateway.yaml
```

### Step 10.2: Remove Istio

```bash
# Remove Istio
istioctl uninstall --purge

# Remove namespace
kubectl delete namespace istio-system
```

---

## Troubleshooting

### Sidecar Not Injected

1. **Check namespace label:**
   ```bash
   kubectl get namespace default -o yaml | grep istio-injection
   ```

2. **Check pod injection:**
   ```bash
   kubectl get pod <pod-name> -o yaml | grep istio-proxy
   ```

3. **Manual injection:**
   ```bash
   istioctl kube-inject -f deployment.yaml | kubectl apply -f -
   ```

### Traffic Not Routing Correctly

1. **Check VirtualService:**
   ```bash
   kubectl get virtualservice
   kubectl describe virtualservice <name>
   ```

2. **Check DestinationRule:**
   ```bash
   kubectl get destinationrule
   kubectl describe destinationrule <name>
   ```

3. **Check Envoy config:**
   ```bash
   istioctl proxy-config route <pod-name>
   ```

### mTLS Issues

1. **Check PeerAuthentication:**
   ```bash
   kubectl get peerauthentication
   ```

2. **Check mTLS status:**
   ```bash
   istioctl authn tls-check <pod-name> <service>
   ```

3. **Check certificates:**
   ```bash
   istioctl proxy-config secret <pod-name>
   ```

---

## Best Practices

1. **Start with Demo Profile:**
   - Use demo profile for learning
   - Switch to minimal for production

2. **Enable mTLS Gradually:**
   - Start with PERMISSIVE mode
   - Move to STRICT mode after testing

3. **Monitor Resources:**
   - Watch proxy resource usage
   - Set appropriate limits

4. **Use GitOps:**
   - Version control all Istio configs
   - Use GitOps for configuration management

5. **Test in Staging:**
   - Always test changes in staging
   - Use canary deployments

---

## Next Steps

- Explore advanced traffic management
- Set up multi-cluster Istio
- Implement custom policies
- Integrate with CI/CD
- Learn about Istio security features

---

## References

- [Istio Documentation](https://istio.io/latest/docs/)
- [Istio Examples](https://istio.io/latest/docs/examples/)
- [Istio Blog](https://istio.io/latest/blog/)

---

**Happy Meshing! 🕸️**

