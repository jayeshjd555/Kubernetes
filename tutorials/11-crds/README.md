# Custom Resource Definitions (CRDs) Tutorial

This tutorial will guide you through creating and using Custom Resource Definitions (CRDs) in Kubernetes.

## Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to access your cluster
- Basic understanding of Kubernetes concepts
- Admin access to the cluster (for creating CRDs)

## Objectives

By the end of this tutorial, you will:
1. Understand what CRDs are and why they're useful
2. Create a Custom Resource Definition
3. Create and manage Custom Resources
4. Understand CRD validation
5. Learn about CRD subresources
6. Explore CRD controllers (conceptually)

---

## Step 1: Create a Simple CRD

Let's create a CRD for managing "Websites" - a custom resource that represents a website deployment.

### Step 1.1: Define the CRD

Create `website-crd.yaml`:

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: websites.example.com
spec:
  group: example.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              required: ["name", "image"]
              properties:
                name:
                  type: string
                  description: "Name of the website"
                image:
                  type: string
                  description: "Container image for the website"
                replicas:
                  type: integer
                  minimum: 1
                  maximum: 10
                  default: 1
                  description: "Number of replicas"
                port:
                  type: integer
                  minimum: 1
                  maximum: 65535
                  default: 80
                  description: "Port number for the website"
            status:
              type: object
              properties:
                phase:
                  type: string
                  enum: ["Pending", "Running", "Failed"]
                message:
                  type: string
                replicas:
                  type: integer
  scope: Namespaced
  names:
    plural: websites
    singular: website
    kind: Website
    shortNames:
      - web
      - ws
```

### Step 1.2: Apply the CRD

```bash
kubectl apply -f website-crd.yaml
```

### Step 1.3: Verify CRD Creation

```bash
kubectl get crd websites.example.com
kubectl describe crd websites.example.com
```

You should see the CRD listed. Check the output:

```bash
kubectl get crd
```

---

## Step 2: Create a Custom Resource

Now let's create an instance of our custom resource.

### Step 2.1: Create Website Resource

Create `my-website.yaml`:

```yaml
apiVersion: example.com/v1
kind: Website
metadata:
  name: my-blog
  namespace: default
spec:
  name: my-blog
  image: nginx:latest
  replicas: 2
  port: 80
```

### Step 2.2: Apply the Custom Resource

```bash
kubectl apply -f my-website.yaml
```

### Step 2.3: Verify Custom Resource

```bash
# List all websites
kubectl get websites
kubectl get web
kubectl get ws

# Get specific website
kubectl get website my-blog

# Get detailed information
kubectl describe website my-blog

# Get YAML output
kubectl get website my-blog -o yaml
```

---

## Step 3: Test CRD Validation

Let's test that our validation rules work correctly.

### Step 3.1: Create Invalid Resource

Create `invalid-website.yaml`:

```yaml
apiVersion: example.com/v1
kind: Website
metadata:
  name: invalid-website
spec:
  # Missing required field "name"
  image: nginx:latest
  replicas: 20  # Exceeds maximum of 10
  port: 70000    # Exceeds maximum of 65535
```

### Step 3.2: Try to Apply Invalid Resource

```bash
kubectl apply -f invalid-website.yaml
```

You should see validation errors:

```
The Website "invalid-website" is invalid:
* spec.name: Required value
* spec.replicas: Invalid value: 20: spec.replicas in body should be less than or equal to 10
* spec.port: Invalid value: 70000: spec.port in body should be less than or equal to 65535
```

### Step 3.3: Fix and Apply

Update `invalid-website.yaml`:

```yaml
apiVersion: example.com/v1
kind: Website
metadata:
  name: invalid-website
spec:
  name: fixed-website
  image: nginx:latest
  replicas: 3
  port: 8080
```

Apply again:

```bash
kubectl apply -f invalid-website.yaml
```

Now it should work!

---

## Step 4: Update Custom Resource

### Step 4.1: Edit Resource

Edit the website resource:

```bash
kubectl edit website my-blog
```

Change `replicas` from 2 to 3, save and exit.

### Step 4.2: Verify Update

```bash
kubectl get website my-blog -o yaml
```

You should see the updated `replicas` value.

### Step 4.3: Patch Resource

Alternatively, use `kubectl patch`:

```bash
kubectl patch website my-blog --type merge -p '{"spec":{"replicas":4}}'
```

Verify:

```bash
kubectl get website my-blog -o jsonpath='{.spec.replicas}'
```

---

## Step 5: Add Status Subresource

Let's enhance our CRD to support status updates.

### Step 5.1: Update CRD with Status Subresource

Update `website-crd.yaml`:

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: websites.example.com
spec:
  group: example.com
  versions:
    - name: v1
      served: true
      storage: true
      subresources:
        status: {}
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              required: ["name", "image"]
              properties:
                name:
                  type: string
                image:
                  type: string
                replicas:
                  type: integer
                  minimum: 1
                  maximum: 10
                  default: 1
                port:
                  type: integer
                  minimum: 1
                  maximum: 65535
                  default: 80
            status:
              type: object
              properties:
                phase:
                  type: string
                  enum: ["Pending", "Running", "Failed"]
                message:
                  type: string
                replicas:
                  type: integer
  scope: Namespaced
  names:
    plural: websites
    singular: website
    kind: Website
    shortNames:
      - web
      - ws
```

### Step 5.2: Update CRD

```bash
kubectl apply -f website-crd.yaml
```

### Step 5.3: Update Status

Update the status of a website:

```bash
kubectl patch website my-blog --type merge -p '{"status":{"phase":"Running","replicas":2,"message":"Website is running"}}'
```

### Step 5.4: Verify Status

```bash
kubectl get website my-blog -o yaml
```

You should see the status field populated.

---

## Step 6: Create Multiple Custom Resources

Let's create a few more website resources to see how they work together.

### Step 6.1: Create Multiple Websites

Create `websites.yaml`:

```yaml
apiVersion: example.com/v1
kind: Website
metadata:
  name: blog
spec:
  name: blog
  image: wordpress:latest
  replicas: 2
  port: 80
---
apiVersion: example.com/v1
kind: Website
metadata:
  name: shop
spec:
  name: shop
  image: nginx:latest
  replicas: 3
  port: 8080
---
apiVersion: example.com/v1
kind: Website
metadata:
  name: api
spec:
  name: api
  image: node:18
  replicas: 1
  port: 3000
```

### Step 6.2: Apply All Websites

```bash
kubectl apply -f websites.yaml
```

### Step 6.3: List All Websites

```bash
kubectl get websites
kubectl get web
```

You should see all three websites listed.

### Step 6.4: Filter Websites

```bash
# Get websites with specific label (if you add labels)
kubectl get websites -l app=web

# Get websites in specific namespace
kubectl get websites -n production
```

---

## Step 7: Delete Custom Resources

### Step 7.1: Delete Single Resource

```bash
kubectl delete website my-blog
```

### Step 7.2: Delete Multiple Resources

```bash
kubectl delete websites --all
```

Or delete by label:

```bash
kubectl delete websites -l app=web
```

### Step 7.3: Verify Deletion

```bash
kubectl get websites
```

---

## Step 8: Advanced CRD Features

### Step 8.1: Add Default Values

Update the CRD to include more default values:

```yaml
spec:
  versions:
    - name: v1
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                name:
                  type: string
                image:
                  type: string
                  default: "nginx:latest"
                replicas:
                  type: integer
                  default: 1
                port:
                  type: integer
                  default: 80
                environment:
                  type: string
                  default: "production"
                  enum: ["development", "staging", "production"]
```

### Step 8.2: Add Additional Properties

Add more fields to the CRD:

```yaml
spec:
  versions:
    - name: v1
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                name:
                  type: string
                image:
                  type: string
                replicas:
                  type: integer
                port:
                  type: integer
                resources:
                  type: object
                  properties:
                    requests:
                      type: object
                      properties:
                        cpu:
                          type: string
                          default: "100m"
                        memory:
                          type: string
                          default: "128Mi"
                    limits:
                      type: object
                      properties:
                        cpu:
                          type: string
                          default: "500m"
                        memory:
                          type: string
                          default: "512Mi"
                env:
                  type: array
                  items:
                    type: object
                    properties:
                      name:
                        type: string
                      value:
                        type: string
```

---

## Step 9: CRD with Finalizers

Let's add finalizers to our CRD for cleanup operations.

### Step 9.1: Create Website with Finalizer

```yaml
apiVersion: example.com/v1
kind: Website
metadata:
  name: finalizer-demo
  finalizers:
    - website.example.com/cleanup
spec:
  name: finalizer-demo
  image: nginx:latest
  replicas: 1
  port: 80
```

### Step 9.2: Apply and Delete

```bash
kubectl apply -f finalizer-demo.yaml
kubectl delete website finalizer-demo
```

### Step 9.3: Check Status

```bash
kubectl get website finalizer-demo
```

The resource will have `deletionTimestamp` set but won't be deleted until the finalizer is removed.

### Step 9.4: Remove Finalizer

```bash
kubectl patch website finalizer-demo --type json -p '[{"op": "remove", "path": "/metadata/finalizers"}]'
```

Now the resource will be deleted.

---

## Step 10: Clean Up

### Step 10.1: Delete All Custom Resources

```bash
kubectl delete websites --all
```

### Step 10.2: Delete CRD

```bash
kubectl delete crd websites.example.com
```

**Note:** Deleting a CRD will also delete all Custom Resources of that type!

---

## Troubleshooting

### CRD Not Appearing

1. **Check CRD creation:**
   ```bash
   kubectl get crd websites.example.com
   ```

2. **Check for errors:**
   ```bash
   kubectl describe crd websites.example.com
   ```

3. **Check API server logs:**
   ```bash
   kubectl logs -n kube-system kube-apiserver-<node-name>
   ```

### Custom Resource Not Creating

1. **Check validation errors:**
   ```bash
   kubectl apply -f my-website.yaml --dry-run=client -o yaml
   ```

2. **Check required fields:**
   ```bash
   kubectl explain website.spec
   ```

3. **Check API availability:**
   ```bash
   kubectl api-resources | grep websites
   ```

### Status Not Updating

1. **Verify status subresource:**
   ```bash
   kubectl get crd websites.example.com -o yaml | grep -A 5 subresources
   ```

2. **Check permissions:**
   - Ensure you have permissions to update status
   - Status updates use PATCH on `/status` endpoint

---

## Best Practices

1. **Naming:**
   - Use clear, descriptive names
   - Follow Kubernetes naming conventions
   - Use domain names for groups (e.g., `example.com`)

2. **Validation:**
   - Always define validation schemas
   - Use required fields appropriately
   - Provide meaningful error messages

3. **Versioning:**
   - Start with `v1alpha1` for new CRDs
   - Plan for version migration
   - Support multiple versions during transition

4. **Status:**
   - Always use status subresource
   - Include observedGeneration
   - Use conditions for state tracking

5. **Documentation:**
   - Add descriptions to all fields
   - Document expected behavior
   - Provide examples

---

## Next Steps

- Learn about CRD controllers
- Explore operator frameworks (Kubebuilder, Operator SDK)
- Build a simple controller
- Study existing operators (Prometheus Operator, Cert-Manager)
- Implement finalizers for cleanup
- Add webhook validation

---

## References

- [Kubernetes CRD Documentation](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/)
- [Kubebuilder Documentation](https://book.kubebuilder.io/)
- [Operator SDK Documentation](https://sdk.operatorframework.io/)

---

**Happy CRD Building! 🚀**

