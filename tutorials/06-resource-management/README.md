# Resource Management Tutorial

This tutorial covers Kubernetes resource management: **Resource Quotas** and **LimitRange**.

## Prerequisites

- Kubernetes cluster running
- `kubectl` installed and configured
- Completed [Core Concepts Tutorial](../01-core-concepts/README.md)

## Tutorial Overview

In this tutorial, you will:
1. Create a namespace for the tutorial
2. Create a ResourceQuota
3. Create a LimitRange
4. Test resource limits
5. Understand resource requests and limits
6. Clean up resources

## Step 1: Create Namespace

**Create a namespace for this tutorial:**
```bash
kubectl apply -f namespace.yml
```

**Verify namespace creation:**
```bash
kubectl get namespace tutorial-resources
```

## Step 2: Create ResourceQuota

**What is a ResourceQuota?**
A ResourceQuota limits the total resource consumption in a namespace.

**Create the ResourceQuota:**
```bash
kubectl apply -f resourcequota.yml
```

**Verify ResourceQuota:**
```bash
kubectl get resourcequota -n tutorial-resources
kubectl describe resourcequota tutorial-quota -n tutorial-resources
```

**Expected output:**
```
NAME            AGE   REQUEST      LIMIT
tutorial-quota  5s    cpu: 0/2     memory: 0/2Gi
```

## Step 3: Create LimitRange

**What is a LimitRange?**
A LimitRange sets default and maximum resource constraints for Pods in a namespace.

**Create the LimitRange:**
```bash
kubectl apply -f limitrange.yml
```

**Verify LimitRange:**
```bash
kubectl get limitrange -n tutorial-resources
kubectl describe limitrange tutorial-limits -n tutorial-resources
```

## Step 4: Create Pods with Resource Requests

**Create a Pod with resource requests:**
```bash
kubectl apply -f pod-with-resources.yml
```

**Verify Pod creation:**
```bash
kubectl get pods -n tutorial-resources
kubectl describe pod resource-pod -n tutorial-resources
```

**Check resource quota usage:**
```bash
kubectl describe resourcequota tutorial-quota -n tutorial-resources
```

## Step 5: Test Resource Limits

**Try to create a Pod that exceeds quota:**
```bash
kubectl apply -f pod-exceeds-quota.yml
```

**Check Pod status (should be Pending):**
```bash
kubectl get pods -n tutorial-resources
kubectl describe pod large-pod -n tutorial-resources
```

**Expected error:**
```
Error: pods "large-pod" is forbidden: exceeded quota: tutorial-quota
```

## Step 6: Test LimitRange Defaults

**Create a Pod without resource specifications:**
```bash
kubectl apply -f pod-no-resources.yml
```

**Check Pod resources (should have defaults from LimitRange):**
```bash
kubectl get pod default-pod -n tutorial-resources -o yaml | grep -A 10 resources
```

## Step 7: Clean Up

**Delete resources:**
```bash
kubectl delete -f pod-no-resources.yml
kubectl delete -f pod-exceeds-quota.yml
kubectl delete -f pod-with-resources.yml
kubectl delete -f limitrange.yml
kubectl delete -f resourcequota.yml
kubectl delete -f namespace.yml
```

## Troubleshooting

**Pod stuck in Pending due to quota:**
```bash
# Check ResourceQuota usage
kubectl describe resourcequota tutorial-quota -n tutorial-resources

# Check Pod events
kubectl describe pod <pod-name> -n tutorial-resources

# Delete unused Pods to free quota
kubectl get pods -n tutorial-resources
```

**Pod rejected due to LimitRange:**
```bash
# Check LimitRange constraints
kubectl describe limitrange tutorial-limits -n tutorial-resources

# Verify Pod resource requests/limits
kubectl get pod <pod-name> -n tutorial-resources -o yaml | grep -A 10 resources
```

## Key Takeaways

1. **ResourceQuota** limits total namespace resources
2. **LimitRange** sets default and max constraints per Pod
3. Pods must specify resource requests when quota exists
4. Resource requests are required for proper scheduling
5. Resource limits prevent resource exhaustion
6. Quotas help with multi-tenancy and cost control
7. Always set appropriate resource requests and limits

## Next Steps

- Learn about Quality of Service (QoS) classes
- Explore resource monitoring
- Practice with different resource scenarios
- Move on to the [Autoscaling Tutorial](../07-autoscaling/README.md)

## Related Documentation

- [Resource Management Documentation](../../docs/08-resource-management.md)
- [Resource Quotas Section](../../docs/08-resource-management.md#resource-quotas-and-limits)
- [LimitRange Section](../../docs/08-resource-management.md#limitrange)

