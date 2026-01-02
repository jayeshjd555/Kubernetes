# Configuration Tutorial

This tutorial covers Kubernetes configuration management: **ConfigMap** and **Secrets**.

## Prerequisites

- Kubernetes cluster running
- `kubectl` installed and configured
- Completed [Core Concepts Tutorial](../01-core-concepts/README.md)

## Tutorial Overview

In this tutorial, you will:
1. Create a ConfigMap
2. Create a Secret
3. Use ConfigMap in a Pod
4. Use Secret in a Pod
5. Update ConfigMap and Secret
6. Clean up resources

## Step 1: Create a ConfigMap

**What is a ConfigMap?**
A ConfigMap stores non-sensitive configuration data as key-value pairs.

**Create the ConfigMap:**
```bash
kubectl apply -f configmap.yml
```

**Verify ConfigMap creation:**
```bash
kubectl get configmaps -n tutorial
kubectl describe configmap app-config -n tutorial
```

**View ConfigMap data:**
```bash
kubectl get configmap app-config -n tutorial -o yaml
```

## Step 2: Create a Secret

**What is a Secret?**
A Secret stores sensitive data like passwords, tokens, or keys.

**Create the Secret:**
```bash
kubectl apply -f secret.yml
```

**Verify Secret creation:**
```bash
kubectl get secrets -n tutorial
kubectl describe secret app-secret -n tutorial
```

**View Secret (encoded):**
```bash
kubectl get secret app-secret -n tutorial -o yaml
```

**Decode Secret values:**
```bash
# Decode username
kubectl get secret app-secret -n tutorial -o jsonpath='{.data.username}' | base64 -d
echo

# Decode password
kubectl get secret app-secret -n tutorial -o jsonpath='{.data.password}' | base64 -d
echo
```

## Step 3: Use ConfigMap in a Pod

**Create a Pod that uses ConfigMap:**
```bash
kubectl apply -f pod-with-configmap.yml
```

**Verify Pod is running:**
```bash
kubectl get pods -n tutorial
kubectl wait --for=condition=Ready pod/config-pod -n tutorial --timeout=60s
```

**Check environment variables from ConfigMap:**
```bash
kubectl exec config-pod -n tutorial -- env | grep APP_
```

**Check ConfigMap mounted as file:**
```bash
kubectl exec config-pod -n tutorial -- cat /etc/config/app.properties
```

## Step 4: Use Secret in a Pod

**Create a Pod that uses Secret:**
```bash
kubectl apply -f pod-with-secret.yml
```

**Verify Pod is running:**
```bash
kubectl get pods -n tutorial
kubectl wait --for=condition=Ready pod/secret-pod -n tutorial --timeout=60s
```

**Check environment variables from Secret:**
```bash
kubectl exec secret-pod -n tutorial -- env | grep DB_
```

**Check Secret mounted as file:**
```bash
kubectl exec secret-pod -n tutorial -- cat /etc/secrets/credentials
```

## Step 5: Update ConfigMap and Secret

**Update ConfigMap:**
```bash
# Edit configmap.yml, change DATABASE_NAME to "production"
kubectl apply -f configmap.yml
```

**Restart Pod to pick up changes:**
```bash
kubectl delete pod config-pod -n tutorial
kubectl apply -f pod-with-configmap.yml
```

**Verify updated values:**
```bash
kubectl exec config-pod -n tutorial -- env | grep APP_DATABASE_NAME
```

**Update Secret:**
```bash
# Edit secret.yml, change password
kubectl apply -f secret.yml

# Restart Pod
kubectl delete pod secret-pod -n tutorial
kubectl apply -f pod-with-secret.yml
```

## Step 6: Clean Up

**Delete resources:**
```bash
kubectl delete -f pod-with-secret.yml
kubectl delete -f pod-with-configmap.yml
kubectl delete -f secret.yml
kubectl delete -f configmap.yml
```

## Troubleshooting

**ConfigMap not available in Pod:**
```bash
# Check ConfigMap exists
kubectl get configmap app-config -n tutorial

# Check Pod configuration
kubectl describe pod config-pod -n tutorial

# Check Pod logs
kubectl logs config-pod -n tutorial
```

**Secret not accessible:**
```bash
# Check Secret exists
kubectl get secret app-secret -n tutorial

# Verify Secret is properly referenced in Pod spec
kubectl get pod secret-pod -n tutorial -o yaml | grep -A 10 secret
```

## Key Takeaways

1. **ConfigMap** stores non-sensitive configuration
2. **Secret** stores sensitive data (base64 encoded)
3. ConfigMaps and Secrets can be used as environment variables
4. ConfigMaps and Secrets can be mounted as files
5. Pods need to be restarted to pick up ConfigMap/Secret changes
6. Secrets are base64 encoded, not encrypted
7. Use Secrets for sensitive data, ConfigMaps for non-sensitive

## Next Steps

- Learn about immutable ConfigMaps and Secrets
- Explore different ways to use ConfigMaps/Secrets
- Practice with real-world scenarios
- Move on to the [Resource Management Tutorial](../06-resource-management/README.md)

## Related Documentation

- [Configuration Documentation](../../docs/07-configuration.md)
- [ConfigMap Section](../../docs/07-configuration.md#configmap)
- [Secrets Section](../../docs/07-configuration.md#secrets)

