# Workloads Tutorial

This tutorial covers Kubernetes workload resources: **Deployments** and **ReplicaSets**.

## Prerequisites

- Kubernetes cluster running
- `kubectl` installed and configured
- Completed [Core Concepts Tutorial](../01-core-concepts/README.md)

## Tutorial Overview

In this tutorial, you will:
1. Create a Deployment
2. Understand ReplicaSet creation
3. Scale the Deployment
4. Perform rolling updates
5. Rollback a deployment
6. Clean up resources

## Step 1: Create a Deployment

**What is a Deployment?**
A Deployment manages Pod replicas and provides declarative updates, rolling updates, and rollbacks.

**Create the Deployment:**
```bash
kubectl apply -f deployment.yml
```

**Verify Deployment creation:**
```bash
kubectl get deployments -n tutorial
kubectl get pods -n tutorial
```

**Expected output:**
```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           10s
```

## Step 2: Understand ReplicaSet

**What is a ReplicaSet?**
A ReplicaSet ensures a specified number of Pod replicas are running.

**View the ReplicaSet created by Deployment:**
```bash
kubectl get replicasets -n tutorial
```

**Describe the ReplicaSet:**
```bash
kubectl describe replicaset -n tutorial
```

## Step 3: Scale the Deployment

**Scale up to 5 replicas:**
```bash
kubectl scale deployment nginx-deployment --replicas=5 -n tutorial
```

**Verify scaling:**
```bash
kubectl get pods -n tutorial
```

**Scale using YAML:**
```bash
# Edit deployment.yml, change replicas to 5
kubectl apply -f deployment.yml
```

## Step 4: Perform Rolling Update

**Update the container image:**
```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.21 -n tutorial
```

**Or update using YAML:**
```bash
# Edit deployment.yml, change image to nginx:1.21
kubectl apply -f deployment.yml
```

**Watch the rolling update:**
```bash
kubectl rollout status deployment/nginx-deployment -n tutorial
```

**View rollout history:**
```bash
kubectl rollout history deployment/nginx-deployment -n tutorial
```

## Step 5: Rollback Deployment

**Rollback to previous version:**
```bash
kubectl rollout undo deployment/nginx-deployment -n tutorial
```

**Rollback to specific revision:**
```bash
# First, view history with details
kubectl rollout history deployment/nginx-deployment -n tutorial --revision=2

# Rollback to revision 2
kubectl rollout undo deployment/nginx-deployment --to-revision=2 -n tutorial
```

## Step 6: Test Deployment Resilience

**Delete a Pod to test self-healing:**
```bash
# Get a Pod name
POD_NAME=$(kubectl get pods -n tutorial -l app=nginx -o jsonpath='{.items[0].metadata.name}')

# Delete the Pod
kubectl delete pod $POD_NAME -n tutorial

# Watch Pod recreation
kubectl get pods -n tutorial -w
```

## Step 7: Clean Up

**Delete the Deployment:**
```bash
kubectl delete deployment nginx-deployment -n tutorial
```

**Or delete using YAML:**
```bash
kubectl delete -f deployment.yml
```

## Troubleshooting

**Deployment not creating Pods:**
```bash
# Check Deployment status
kubectl describe deployment nginx-deployment -n tutorial

# Check ReplicaSet
kubectl describe replicaset -n tutorial

# Check Pod events
kubectl get events -n tutorial
```

**Rolling update stuck:**
```bash
# Check rollout status
kubectl rollout status deployment/nginx-deployment -n tutorial

# View Deployment events
kubectl describe deployment nginx-deployment -n tutorial
```

## Key Takeaways

1. **Deployments** manage Pod replicas and updates
2. **ReplicaSets** ensure desired number of replicas
3. Deployments automatically create ReplicaSets
4. Use `kubectl scale` to change replica count
5. Rolling updates provide zero-downtime updates
6. Use `kubectl rollout undo` to rollback
7. Deployments provide self-healing capabilities

## Next Steps

- Try creating a StatefulSet for stateful applications
- Learn about DaemonSets for node-specific workloads
- Explore Jobs and CronJobs for batch processing
- Move on to the [Storage Tutorial](../03-storage/README.md)

## Related Documentation

- [Workloads Documentation](../../docs/04-workloads.md)
- [Deployments Section](../../docs/04-workloads.md#deployments)
- [ReplicaSet Section](../../docs/04-workloads.md#replicaset)

