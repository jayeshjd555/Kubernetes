# Storage Tutorial

This tutorial covers Kubernetes storage concepts: **PersistentVolume**, **PersistentVolumeClaim**, and **StorageClass**.

## Prerequisites

- Kubernetes cluster running
- `kubectl` installed and configured
- Completed [Workloads Tutorial](../02-workloads/README.md)

## Tutorial Overview

In this tutorial, you will:
1. Create a PersistentVolume
2. Create a PersistentVolumeClaim
3. Create a Pod that uses the PVC
4. Verify data persistence
5. Clean up resources

## Step 1: Create a PersistentVolume

**What is a PersistentVolume?**
A PersistentVolume (PV) is a cluster-wide storage resource provisioned by an administrator.

**Create the PersistentVolume:**
```bash
kubectl apply -f persistentvolume.yml
```

**Verify PV creation:**
```bash
kubectl get pv
kubectl describe pv tutorial-pv
```

**Expected output:**
```
NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   AGE
tutorial-pv   1Gi        RWO            Retain           Available           local-storage  5s
```

## Step 2: Create a PersistentVolumeClaim

**What is a PersistentVolumeClaim?**
A PersistentVolumeClaim (PVC) is a request for storage by a user. It binds to a PV.

**Create the PersistentVolumeClaim:**
```bash
kubectl apply -f persistentvolumeclaim.yml
```

**Verify PVC creation and binding:**
```bash
kubectl get pvc -n tutorial
kubectl describe pvc tutorial-pvc -n tutorial
```

**Expected output:**
```
NAME          STATUS   VOLUME        CAPACITY   ACCESS MODES   STORAGECLASS   AGE
tutorial-pvc Bound    tutorial-pv   1Gi        RWO            local-storage  10s
```

**Check PV status (should be Bound):**
```bash
kubectl get pv
```

## Step 3: Create a Pod Using PVC

**Create a Pod that uses the PVC:**
```bash
kubectl apply -f pod-with-pvc.yml
```

**Verify Pod creation:**
```bash
kubectl get pods -n tutorial
```

**Wait for Pod to be Running:**
```bash
kubectl wait --for=condition=Ready pod/data-pod -n tutorial --timeout=60s
```

## Step 4: Write Data to Persistent Storage

**Execute a command to write data:**
```bash
kubectl exec -it data-pod -n tutorial -- sh -c "echo 'Hello from Persistent Storage!' > /data/hello.txt"
```

**Verify data was written:**
```bash
kubectl exec -it data-pod -n tutorial -- cat /data/hello.txt
```

## Step 5: Test Data Persistence

**Delete the Pod:**
```bash
kubectl delete pod data-pod -n tutorial
```

**Create a new Pod with the same PVC:**
```bash
kubectl apply -f pod-with-pvc.yml
```

**Wait for Pod to be Ready:**
```bash
kubectl wait --for=condition=Ready pod/data-pod -n tutorial --timeout=60s
```

**Verify data persists:**
```bash
kubectl exec -it data-pod -n tutorial -- cat /data/hello.txt
```

**Expected output:**
```
Hello from Persistent Storage!
```

## Step 6: Clean Up

**Delete the Pod:**
```bash
kubectl delete pod data-pod -n tutorial
```

**Delete the PVC:**
```bash
kubectl delete pvc tutorial-pvc -n tutorial
```

**Delete the PV:**
```bash
kubectl delete pv tutorial-pv
```

**Or delete all using YAML files:**
```bash
kubectl delete -f pod-with-pvc.yml
kubectl delete -f persistentvolumeclaim.yml
kubectl delete -f persistentvolume.yml
```

## Troubleshooting

**PVC stuck in Pending:**
```bash
# Check PVC status
kubectl describe pvc tutorial-pvc -n tutorial

# Check available PVs
kubectl get pv

# Verify PV and PVC match (storage class, access modes, size)
```

**Pod cannot mount volume:**
```bash
# Check Pod events
kubectl describe pod data-pod -n tutorial

# Verify PVC is bound
kubectl get pvc -n tutorial
```

## Key Takeaways

1. **PersistentVolume** provides cluster-wide storage
2. **PersistentVolumeClaim** requests storage from PVs
3. PVCs bind to matching PVs automatically
4. Data persists across Pod restarts
5. PVs can be reused by different Pods
6. Always clean up PVCs before PVs

## Next Steps

- Learn about StorageClasses for dynamic provisioning
- Explore different access modes (RWO, ROX, RWX)
- Try different storage backends
- Move on to the [Networking Tutorial](../04-networking/README.md)

## Related Documentation

- [Storage Documentation](../../docs/05-storage.md)
- [PersistentVolume Section](../../docs/05-storage.md#persistentvolume-pv)
- [PersistentVolumeClaim Section](../../docs/05-storage.md#persistentvolumeclaim-pvc)

