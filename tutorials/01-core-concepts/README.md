# Core Concepts Tutorial

This tutorial covers fundamental Kubernetes concepts: **Namespaces** and **Pods**.

## Prerequisites

- Kubernetes cluster running (Minikube, KIND, or cloud cluster)
- `kubectl` installed and configured
- Basic understanding of Kubernetes concepts

## Tutorial Overview

In this tutorial, you will:
1. Create a namespace
2. Create a Pod in that namespace
3. Verify Pod status
4. Access the Pod
5. Clean up resources

## Step 1: Create a Namespace

**What is a Namespace?**
A namespace provides a way to organize and isolate resources in a Kubernetes cluster.

**Create the namespace:**
```bash
kubectl apply -f namespace.yml
```

**Verify namespace creation:**
```bash
kubectl get namespaces
kubectl get ns tutorial
```

**Expected output:**
```
NAME       STATUS   AGE
tutorial   Active   5s
```

## Step 2: Create a Pod

**What is a Pod?**
A Pod is the smallest deployable unit in Kubernetes. It contains one or more containers.

**Create the Pod:**
```bash
kubectl apply -f pod.yml
```

**Verify Pod creation:**
```bash
kubectl get pods -n tutorial
```

**Expected output:**
```
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          10s
```

## Step 3: Inspect Pod Details

**Get detailed Pod information:**
```bash
kubectl describe pod nginx-pod -n tutorial
```

**Get Pod YAML:**
```bash
kubectl get pod nginx-pod -n tutorial -o yaml
```

## Step 4: Access the Pod

**Execute a command in the Pod:**
```bash
kubectl exec -it nginx-pod -n tutorial -- /bin/sh
```

**Inside the Pod, you can run:**
```bash
# Check nginx is running
ps aux | grep nginx

# Check nginx version
nginx -v

# Exit the Pod
exit
```

**Port forward to access nginx:**
```bash
kubectl port-forward pod/nginx-pod 8080:80 -n tutorial
```

**In another terminal, test nginx:**
```bash
curl http://localhost:8080
```

## Step 5: View Pod Logs

**View Pod logs:**
```bash
kubectl logs nginx-pod -n tutorial
```

**Follow logs in real-time:**
```bash
kubectl logs -f nginx-pod -n tutorial
```

## Step 6: Clean Up

**Delete the Pod:**
```bash
kubectl delete pod nginx-pod -n tutorial
```

**Delete the namespace (this will delete all resources in the namespace):**
```bash
kubectl delete namespace tutorial
```

**Or delete using YAML files:**
```bash
kubectl delete -f pod.yml
kubectl delete -f namespace.yml
```

## Troubleshooting

**Pod stuck in Pending:**
```bash
# Check Pod events
kubectl describe pod nginx-pod -n tutorial

# Check node resources
kubectl get nodes
```

**Pod in CrashLoopBackOff:**
```bash
# Check Pod logs
kubectl logs nginx-pod -n tutorial

# Check previous container logs
kubectl logs nginx-pod -n tutorial --previous
```

**Cannot access Pod:**
```bash
# Verify Pod is running
kubectl get pods -n tutorial

# Check Pod status
kubectl describe pod nginx-pod -n tutorial
```

## Key Takeaways

1. **Namespaces** organize and isolate resources
2. **Pods** are the smallest deployable units
3. Use `kubectl apply` to create resources
4. Use `kubectl get` to view resources
5. Use `kubectl describe` for detailed information
6. Use `kubectl exec` to access Pods
7. Use `kubectl logs` to view Pod logs
8. Always clean up resources after tutorials

## Next Steps

- Try creating multiple Pods in the same namespace
- Experiment with different container images
- Learn about Pod lifecycle and states
- Move on to the [Workloads Tutorial](../02-workloads/README.md)

## Related Documentation

- [Core Concepts Documentation](../../docs/03-core-concepts.md)
- [Namespaces Section](../../docs/03-core-concepts.md#namespaces)
- [Pods Section](../../docs/03-core-concepts.md#pods)

