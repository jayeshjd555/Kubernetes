# Advanced Topics Tutorial

This tutorial covers advanced Kubernetes topics: **RBAC** and **Node Affinity**.

## Prerequisites

- Kubernetes cluster running
- `kubectl` installed and configured
- Cluster admin access (for RBAC)
- Multiple nodes (for Node Affinity - optional)
- Completed previous tutorials

## Tutorial Overview

In this tutorial, you will:
1. Create a ServiceAccount
2. Create a Role and RoleBinding
3. Test RBAC permissions
4. Label nodes for Node Affinity
5. Create Pods with Node Affinity
6. Clean up resources

## Part 1: RBAC Tutorial

### Step 1: Create ServiceAccount

**Create a ServiceAccount:**
```bash
kubectl apply -f serviceaccount.yml
```

**Verify ServiceAccount:**
```bash
kubectl get serviceaccount -n tutorial
kubectl describe serviceaccount app-sa -n tutorial
```

### Step 2: Create Role

**Create a Role with specific permissions:**
```bash
kubectl apply -f role.yml
```

**Verify Role:**
```bash
kubectl get roles -n tutorial
kubectl describe role pod-reader -n tutorial
```

### Step 3: Create RoleBinding

**Bind the Role to the ServiceAccount:**
```bash
kubectl apply -f rolebinding.yml
```

**Verify RoleBinding:**
```bash
kubectl get rolebindings -n tutorial
kubectl describe rolebinding pod-reader-binding -n tutorial
```

### Step 4: Test RBAC Permissions

**Create a Pod using the ServiceAccount:**
```bash
kubectl apply -f pod-with-sa.yml
```

**Wait for Pod to be Ready:**
```bash
kubectl wait --for=condition=Ready pod/rbac-test-pod -n tutorial --timeout=60s
```

**Test permissions from within the Pod:**
```bash
# Test reading Pods (should work)
kubectl exec rbac-test-pod -n tutorial -- sh -c "curl -k https://kubernetes.default/api/v1/namespaces/tutorial/pods" || echo "Access denied or API not accessible"

# Test creating a Pod (should fail)
kubectl exec rbac-test-pod -n tutorial -- sh -c "curl -X POST -k https://kubernetes.default/api/v1/namespaces/tutorial/pods" || echo "Expected: Access denied"
```

**Test using kubectl auth can-i:**
```bash
# Test as ServiceAccount
kubectl auth can-i get pods --as=system:serviceaccount:tutorial:app-sa -n tutorial

# Test creating pods (should be false)
kubectl auth can-i create pods --as=system:serviceaccount:tutorial:app-sa -n tutorial

# List all permissions
kubectl auth can-i --list --as=system:serviceaccount:tutorial:app-sa -n tutorial
```

## Part 2: Node Affinity Tutorial

### Step 1: Label Nodes

**List nodes:**
```bash
kubectl get nodes
```

**Label nodes (replace node-name with actual node name):**
```bash
# Label node 1
kubectl label nodes <node-name-1> zone=us-east-1 instance-type=small

# Label node 2 (if available)
kubectl label nodes <node-name-2> zone=us-west-1 instance-type=large

# Verify labels
kubectl get nodes --show-labels
```

**For single-node clusters (Minikube/KIND):**
```bash
# Get node name
NODE_NAME=$(kubectl get nodes -o jsonpath='{.items[0].metadata.name}')

# Label the node
kubectl label nodes $NODE_NAME zone=us-east-1 instance-type=small
```

### Step 2: Create Pod with Required Node Affinity

**Create a Pod that must run on specific zone:**
```bash
kubectl apply -f pod-required-affinity.yml
```

**Verify Pod scheduling:**
```bash
kubectl get pods -n tutorial -o wide
kubectl describe pod affinity-pod -n tutorial
```

**Check which node the Pod is on:**
```bash
kubectl get pod affinity-pod -n tutorial -o jsonpath='{.spec.nodeName}'
```

### Step 3: Create Pod with Preferred Node Affinity

**Create a Pod with preferred affinity:**
```bash
kubectl apply -f pod-preferred-affinity.yml
```

**Verify Pod scheduling:**
```bash
kubectl get pods -n tutorial -o wide
kubectl describe pod preferred-affinity-pod -n tutorial
```

### Step 4: Test Anti-Affinity

**Create a Pod that avoids specific zone:**
```bash
kubectl apply -f pod-anti-affinity.yml
```

**Verify Pod scheduling:**
```bash
kubectl get pods -n tutorial -o wide
```

## Step 5: Clean Up

**Delete RBAC resources:**
```bash
kubectl delete -f pod-with-sa.yml
kubectl delete -f rolebinding.yml
kubectl delete -f role.yml
kubectl delete -f serviceaccount.yml
```

**Delete Node Affinity Pods:**
```bash
kubectl delete -f pod-anti-affinity.yml
kubectl delete -f pod-preferred-affinity.yml
kubectl delete -f pod-required-affinity.yml
```

**Remove node labels (optional):**
```bash
kubectl label nodes <node-name> zone-
kubectl label nodes <node-name> instance-type-
```

## Troubleshooting

**RBAC: Permission denied:**
```bash
# Check ServiceAccount exists
kubectl get serviceaccount app-sa -n tutorial

# Check Role and RoleBinding
kubectl describe role pod-reader -n tutorial
kubectl describe rolebinding pod-reader-binding -n tutorial

# Test permissions
kubectl auth can-i get pods --as=system:serviceaccount:tutorial:app-sa -n tutorial
```

**Node Affinity: Pod stuck in Pending:**
```bash
# Check Pod events
kubectl describe pod affinity-pod -n tutorial

# Verify node labels match affinity
kubectl get nodes --show-labels

# Check if nodes match requirements
kubectl get nodes -l zone=us-east-1
```

## Key Takeaways

1. **RBAC** controls access to Kubernetes resources
2. **ServiceAccounts** are used for Pod permissions
3. **Roles** define permissions within a namespace
4. **RoleBindings** connect Roles to ServiceAccounts
5. **Node Affinity** controls Pod scheduling based on node labels
6. **Required affinity** is a hard requirement
7. **Preferred affinity** is a soft preference
8. Always test RBAC permissions before deploying

## Next Steps

- Learn about ClusterRoles and ClusterRoleBindings
- Explore Pod Affinity and Anti-Affinity
- Practice with Taints and Tolerations
- Review all tutorials and concepts

## Related Documentation

- [Advanced Topics Documentation](../../docs/10-advanced.md)
- [RBAC Section](../../docs/10-advanced.md#rbac---role-based-access-control)
- [Node Affinity Section](../../docs/10-advanced.md#node-affinity)

