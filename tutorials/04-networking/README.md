# Networking Tutorial

This tutorial covers Kubernetes networking: **Services** and **Ingress**.

## Prerequisites

- Kubernetes cluster running
- `kubectl` installed and configured
- Ingress controller installed (for Ingress tutorial)
- Completed [Workloads Tutorial](../02-workloads/README.md)

## Tutorial Overview

In this tutorial, you will:
1. Create a Deployment with Pods
2. Create a Service to expose Pods
3. Access the Service
4. Create an Ingress resource
5. Access the application via Ingress
6. Clean up resources

## Step 1: Create a Deployment

**Create the Deployment:**
```bash
kubectl apply -f deployment.yml
```

**Verify Deployment and Pods:**
```bash
kubectl get deployments -n tutorial
kubectl get pods -n tutorial
```

**Wait for Pods to be Ready:**
```bash
kubectl wait --for=condition=Ready pod -l app=web -n tutorial --timeout=60s
```

## Step 2: Create a Service

**What is a Service?**
A Service provides a stable endpoint to access Pods. It load balances traffic across Pods.

**Create the Service:**
```bash
kubectl apply -f service.yml
```

**Verify Service creation:**
```bash
kubectl get services -n tutorial
kubectl describe service web-service -n tutorial
```

**Expected output:**
```
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
web-service   ClusterIP   10.96.123.45    <none>        80/TCP    10s
```

## Step 3: Access the Service

**Port forward to access the Service:**
```bash
kubectl port-forward service/web-service 8080:80 -n tutorial
```

**In another terminal, test the Service:**
```bash
curl http://localhost:8080
```

**Access Service from within cluster:**
```bash
# Create a temporary Pod
kubectl run -it --rm debug --image=curlimages/curl --restart=Never -n tutorial -- curl http://web-service:80

# Or use Service DNS name
kubectl run -it --rm debug --image=curlimages/curl --restart=Never -n tutorial -- curl http://web-service.tutorial.svc.cluster.local:80
```

## Step 4: Verify Load Balancing

**Check Service endpoints:**
```bash
kubectl get endpoints web-service -n tutorial
```

**Scale the Deployment:**
```bash
kubectl scale deployment web-deployment --replicas=5 -n tutorial
```

**Verify endpoints updated:**
```bash
kubectl get endpoints web-service -n tutorial
```

## Step 5: Create an Ingress (Optional)

**What is an Ingress?**
An Ingress provides HTTP/HTTPS routing to Services. It requires an Ingress Controller.

**Check if Ingress Controller is installed:**
```bash
kubectl get pods -n ingress-nginx
# or
kubectl get pods -n kube-system | grep ingress
```

**Create the Ingress:**
```bash
kubectl apply -f ingress.yml
```

**Verify Ingress creation:**
```bash
kubectl get ingress -n tutorial
kubectl describe ingress web-ingress -n tutorial
```

**Get Ingress IP/Address:**
```bash
kubectl get ingress web-ingress -n tutorial
```

**Access via Ingress:**
```bash
# If using Minikube
minikube service web-service -n tutorial

# Or get the Ingress IP and access directly
INGRESS_IP=$(kubectl get ingress web-ingress -n tutorial -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
curl http://$INGRESS_IP
```

## Step 6: Clean Up

**Delete resources:**
```bash
kubectl delete -f ingress.yml
kubectl delete -f service.yml
kubectl delete -f deployment.yml
```

## Troubleshooting

**Service not accessible:**
```bash
# Check Service endpoints
kubectl get endpoints web-service -n tutorial

# Verify Pods are running
kubectl get pods -n tutorial

# Check Service selector matches Pod labels
kubectl describe service web-service -n tutorial
```

**Ingress not working:**
```bash
# Check Ingress Controller is running
kubectl get pods -n ingress-nginx

# Check Ingress status
kubectl describe ingress web-ingress -n tutorial

# Check Ingress Controller logs
kubectl logs -n ingress-nginx -l app.kubernetes.io/component=controller
```

## Key Takeaways

1. **Services** provide stable endpoints for Pods
2. Services load balance traffic across Pods
3. Services use selectors to find Pods
4. **Ingress** provides HTTP/HTTPS routing
5. Ingress requires an Ingress Controller
6. Services can be accessed via DNS names
7. Different Service types (ClusterIP, NodePort, LoadBalancer)

## Next Steps

- Try different Service types (NodePort, LoadBalancer)
- Configure TLS/SSL with Ingress
- Learn about Service Discovery
- Move on to the [Configuration Tutorial](../05-configuration/README.md)

## Related Documentation

- [Networking Documentation](../../docs/06-networking.md)
- [Services Section](../../docs/06-networking.md#services)
- [Ingress Section](../../docs/06-networking.md#ingress)

