# Autoscaling Tutorial

This tutorial covers Kubernetes autoscaling: **Horizontal Pod Autoscaler (HPA)**.

## Prerequisites

- Kubernetes cluster running
- `kubectl` installed and configured
- Metrics Server installed (required for HPA)
- Completed [Workloads Tutorial](../02-workloads/README.md)

## Tutorial Overview

In this tutorial, you will:
1. Install Metrics Server (if not installed)
2. Create a Deployment with resource requests
3. Create an HPA
4. Generate load to trigger scaling
5. Observe autoscaling in action
6. Clean up resources

## Step 1: Install Metrics Server

**Check if Metrics Server is installed:**
```bash
kubectl get deployment metrics-server -n kube-system
```

**If not installed, install Metrics Server:**
```bash
# For Minikube
minikube addons enable metrics-server

# For other clusters, install from official manifests
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

**Verify Metrics Server is running:**
```bash
kubectl get pods -n kube-system | grep metrics-server
kubectl top nodes
kubectl top pods -A
```

## Step 2: Create Deployment with Resource Requests

**Important:** HPA requires resource requests to calculate CPU/memory utilization.

**Create the Deployment:**
```bash
kubectl apply -f deployment.yml
```

**Verify Deployment:**
```bash
kubectl get deployments -n tutorial
kubectl get pods -n tutorial
```

**Wait for Pods to be Ready:**
```bash
kubectl wait --for=condition=Ready pod -l app=webapp -n tutorial --timeout=60s
```

## Step 3: Create HPA

**What is HPA?**
HPA automatically scales the number of Pods based on CPU or memory utilization.

**Create the HPA:**
```bash
kubectl apply -f hpa.yml
```

**Verify HPA creation:**
```bash
kubectl get hpa -n tutorial
kubectl describe hpa webapp-hpa -n tutorial
```

**Expected output:**
```
NAME          REFERENCE                TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
webapp-hpa    Deployment/webapp-deploy   0%/50%    1         5         1          10s
```

## Step 4: Generate Load to Trigger Scaling

**Option 1: Using kubectl run (simple load):**
```bash
# Create a Pod that generates CPU load
kubectl run -it --rm load-generator --image=busybox --restart=Never -n tutorial -- sh -c "while true; do wget -q -O- http://webapp-service:80; done"
```

**Option 2: Using a load testing tool:**
```bash
# Install hey (HTTP load testing tool)
# Or use any load testing tool

# Generate load
hey -n 10000 -c 10 http://<service-ip>:80
```

**Option 3: Using a dedicated load Pod:**
```bash
kubectl apply -f load-generator.yml
```

**Watch HPA and Pods:**
```bash
# Watch HPA
kubectl get hpa webapp-hpa -n tutorial -w

# Watch Pods
kubectl get pods -n tutorial -w
```

**Expected behavior:**
- HPA detects high CPU usage
- HPA scales up Pods (increases replicas)
- You should see Pods being created

## Step 5: Observe Autoscaling

**Check HPA status:**
```bash
kubectl describe hpa webapp-hpa -n tutorial
```

**View current metrics:**
```bash
kubectl top pods -n tutorial
```

**Check HPA events:**
```bash
kubectl get events -n tutorial --sort-by='.lastTimestamp' | grep hpa
```

**Stop the load:**
```bash
# Stop load generator
kubectl delete pod load-generator -n tutorial
# or
kubectl delete -f load-generator.yml
```

**Observe scale down:**
```bash
# Watch HPA scale down
kubectl get hpa webapp-hpa -n tutorial -w
kubectl get pods -n tutorial -w
```

## Step 6: Clean Up

**Delete resources:**
```bash
kubectl delete -f hpa.yml
kubectl delete -f deployment.yml
kubectl delete -f service.yml
```

## Troubleshooting

**HPA shows "unknown" metrics:**
```bash
# Check Metrics Server is running
kubectl get pods -n kube-system | grep metrics-server

# Check Metrics Server logs
kubectl logs -n kube-system -l k8s-app=metrics-server

# Verify resource requests are set
kubectl get deployment webapp-deploy -n tutorial -o yaml | grep -A 10 resources
```

**HPA not scaling:**
```bash
# Check HPA status
kubectl describe hpa webapp-hpa -n tutorial

# Check current metrics
kubectl top pods -n tutorial

# Verify Deployment has resource requests
kubectl describe deployment webapp-deploy -n tutorial
```

**Pods not scaling up:**
```bash
# Check if maxReplicas is reached
kubectl get hpa webapp-hpa -n tutorial

# Check for resource constraints
kubectl describe nodes

# Check Pod events
kubectl get events -n tutorial
```

## Key Takeaways

1. **HPA** automatically scales Pods based on metrics
2. Metrics Server is required for CPU/memory metrics
3. Resource requests are required for HPA to work
4. HPA scales between minReplicas and maxReplicas
5. HPA checks metrics every 15 seconds by default
6. Scale-up is faster than scale-down
7. HPA works with Deployments, ReplicaSets, and StatefulSets

## Next Steps

- Learn about VPA (Vertical Pod Autoscaler)
- Explore KEDA for event-driven autoscaling
- Configure custom metrics for HPA
- Move on to the [Advanced Tutorial](../08-advanced/README.md)

## Related Documentation

- [Autoscaling Documentation](../../docs/09-autoscaling.md)
- [HPA Section](../../docs/09-autoscaling.md#hpa---horizontal-pod-autoscaler)

