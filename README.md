# Kubernetes Notes

> **📌 Note:** This documentation has been split into multiple files for better readability and GitHub rendering. Each section is in a separate file in the `docs/` directory.

## Table of Contents

1. [Introduction & Overview](./docs/01-introduction.md#introduction--overview-of-kubernetes-concepts)
   - [Kubernetes History](./docs/01-introduction.md#kubernetes-history)
   - [Why Should We Learn Kubernetes?](./docs/01-introduction.md#why-should-we-learn-kubernetes)
   - [Monolithic vs Microservices Architecture](./docs/01-introduction.md#monolithic-vs-microservices-architecture)

2. [Kubernetes Architecture & Setup](./docs/02-architecture.md#kubernetes-architecture)
   - [Kubernetes Architecture](./docs/02-architecture.md#kubernetes-architecture)
   - [Kubernetes Setup](./docs/02-architecture.md#kubernetes-setup)

3. [Core Concepts](./docs/03-core-concepts.md#kubernetes-concepts)
   - [Kubernetes Objects and its Management](./docs/03-core-concepts.md#kubernetes-objects-and-its-management)
   - [Field Selectors](./docs/03-core-concepts.md#field-selectors)
   - [Namespaces](./docs/03-core-concepts.md#namespaces)
   - [Pods](./docs/03-core-concepts.md#pods)
   - [Labels and Selectors](./docs/03-core-concepts.md#labels-and-selectors)
   - [Annotations](./docs/03-core-concepts.md#annotations)

4. [Workloads](./docs/04-workloads.md)
   - [ReplicaSet](./docs/04-workloads.md#replicaset)
   - [Deployments](./docs/04-workloads.md#deployments)
   - [StatefulSet](./docs/04-workloads.md#statefulset)
   - [DaemonSet](./docs/04-workloads.md#daemonset)
   - [Jobs](./docs/04-workloads.md#jobs)
   - [CronJobs](./docs/04-workloads.md#cronjobs)

5. [Storage](./docs/05-storage.md#storage)
   - [StorageClass](./docs/05-storage.md#storageclass)
   - [PersistentVolume (PV)](./docs/05-storage.md#persistentvolume-pv)
   - [PersistentVolumeClaim (PVC)](./docs/05-storage.md#persistentvolumeclaim-pvc)

6. [Networking](./docs/06-networking.md)
   - [Services](./docs/06-networking.md#services)
   - [Ingress](./docs/06-networking.md#ingress)

7. [Configuration](./docs/07-configuration.md)
   - [ConfigMap](./docs/07-configuration.md#configmap)
   - [Secrets](./docs/07-configuration.md#secrets)

8. [Resource Management](./docs/08-resource-management.md)
   - [Resource Quotas and Limits](./docs/08-resource-management.md#resource-quotas-and-limits)
   - [Probes](./docs/08-resource-management.md#probes)

9. [Autoscaling](./docs/09-autoscaling.md#autoscaling)
   - [HPA - Horizontal Pod Autoscaler](./docs/09-autoscaling.md#hpa---horizontal-pod-autoscaler)
   - [VPA - Vertical Pod Autoscaler](./docs/09-autoscaling.md#vpa---vertical-pod-autoscaler)
   - [KEDA - Kubernetes Event-Driven Autoscaling](./docs/09-autoscaling.md#keda---kubernetes-event-driven-autoscaling)
   - [Cluster Autoscaler](./docs/09-autoscaling.md#cluster-autoscaler)
   - [Karpenter](./docs/09-autoscaling.md#karpenter)

10. [Advanced Topics](./docs/10-advanced.md)
    - [Taints and Tolerations](./docs/10-advanced.md#taints-and-tolerations)
    - [Node Affinity](./docs/10-advanced.md#node-affinity)
    - [RBAC - Role-Based Access Control](./docs/10-advanced.md#rbac---role-based-access-control)

11. [Logging and Monitoring](./docs/11-logging-monitoring.md)
    - [Kubernetes Logging](./docs/11-logging-monitoring.md#kubernetes-logging)
    - [Kubernetes Monitoring](./docs/11-logging-monitoring.md#kubernetes-monitoring)
    - [Observability Stack](./docs/11-logging-monitoring.md#observability-stack)

12. [Custom Resource Definitions (CRDs)](./docs/12-crds.md)
    - [What are CRDs?](./docs/12-crds.md#what-are-crds)
    - [Creating CRDs](./docs/12-crds.md#creating-crds)
    - [CRD Validation](./docs/12-crds.md#crd-validation)
    - [CRD Controllers](./docs/12-crds.md#crd-controllers)
    - [Operator Pattern](./docs/12-crds.md#operator-pattern)

13. [Package Management and Templating](./docs/13-package-management.md)
    - [Helm - The Package Manager for Kubernetes](./docs/13-package-management.md#helm---the-package-manager-for-kubernetes)
    - [Kustomize - Kubernetes Native Configuration Management](./docs/13-package-management.md#kustomize---kubernetes-native-configuration-management)
    - [Helm vs Kustomize](./docs/13-package-management.md#helm-vs-kustomize)
    - [Other Package Management Tools](./docs/13-package-management.md#other-package-management-tools)

---

## Quick Navigation

- 📚 [All Documentation](./docs/)
- 🏗️ [Architecture & Setup](./docs/02-architecture.md)
- 💼 [Workloads](./docs/04-workloads.md)
- 🌐 [Networking](./docs/06-networking.md)
- ⚙️ [Configuration](./docs/07-configuration.md)
- 📈 [Autoscaling](./docs/09-autoscaling.md)
- 📊 [Logging and Monitoring](./docs/11-logging-monitoring.md)

---

## 🎓 Hands-On Tutorials

Practice what you learn with step-by-step tutorials:

1. **[Core Concepts Tutorial](./tutorials/01-core-concepts/README.md)** - Namespaces and Pods
2. **[Workloads Tutorial](./tutorials/02-workloads/README.md)** - Deployments and ReplicaSets
3. **[Storage Tutorial](./tutorials/03-storage/README.md)** - PersistentVolumes and PersistentVolumeClaims
4. **[Networking Tutorial](./tutorials/04-networking/README.md)** - Services and Ingress
5. **[Configuration Tutorial](./tutorials/05-configuration/README.md)** - ConfigMaps and Secrets
6. **[Resource Management Tutorial](./tutorials/06-resource-management/README.md)** - Resource Quotas and Limits
7. **[Autoscaling Tutorial](./tutorials/07-autoscaling/README.md)** - Horizontal Pod Autoscaler (HPA)
8. **[Advanced Topics Tutorial](./tutorials/08-advanced/README.md)** - RBAC and Node Affinity
9. **[Logging and Monitoring Tutorial](./tutorials/09-logging-monitoring/README.md)** - Log Collection and Monitoring Setup
10. **[Kubernetes Dashboard Tutorial](./tutorials/10-kubernetes-dashboard/README.md)** - Install and Use Kubernetes Dashboard
11. **[CRDs Tutorial](./tutorials/11-crds/README.md)** - Create and Use Custom Resource Definitions
12. **[Helm Tutorial](./tutorials/12-helm/README.md)** - Create and Use Helm Charts
13. **[Kustomize Tutorial](./tutorials/13-kustomize/README.md)** - Manage Configurations with Kustomize

Each tutorial includes:
- Step-by-step instructions
- YAML files ready to use
- Troubleshooting guides
- Best practices

---

## Repository Structure

```
Kubernetes/
├── README.md (this file)
├── docs/
│   ├── 01-introduction.md
│   ├── 02-architecture.md
│   ├── 03-core-concepts.md
│   ├── 04-workloads.md
│   ├── 05-storage.md
│   ├── 06-networking.md
│   ├── 07-configuration.md
│   ├── 08-resource-management.md
│   ├── 09-autoscaling.md
│   ├── 10-advanced.md
│   ├── 11-logging-monitoring.md
│   ├── 12-crds.md
│   └── 13-package-management.md
├── tutorials/
│   ├── 01-core-concepts/
│   │   ├── README.md
│   │   ├── namespace.yml
│   │   └── pod.yml
│   ├── 02-workloads/
│   │   ├── README.md
│   │   └── deployment.yml
│   ├── 03-storage/
│   │   ├── README.md
│   │   ├── persistentvolume.yml
│   │   ├── persistentvolumeclaim.yml
│   │   └── pod-with-pvc.yml
│   ├── 04-networking/
│   │   ├── README.md
│   │   ├── deployment.yml
│   │   ├── service.yml
│   │   └── ingress.yml
│   ├── 05-configuration/
│   │   ├── README.md
│   │   ├── configmap.yml
│   │   ├── secret.yml
│   │   ├── pod-with-configmap.yml
│   │   └── pod-with-secret.yml
│   ├── 06-resource-management/
│   │   ├── README.md
│   │   ├── namespace.yml
│   │   ├── resourcequota.yml
│   │   ├── limitrange.yml
│   │   └── pod-with-resources.yml
│   ├── 07-autoscaling/
│   │   ├── README.md
│   │   ├── deployment.yml
│   │   ├── service.yml
│   │   ├── hpa.yml
│   │   └── load-generator.yml
│   └── 08-advanced/
│       ├── README.md
│       ├── serviceaccount.yml
│       ├── role.yml
│       ├── rolebinding.yml
│       └── pod-with-sa.yml
│   └── 09-logging-monitoring/
│       ├── README.md
│       ├── namespace.yml
│       ├── fluentd-daemonset.yml
│       ├── prometheus-deployment.yml
│       └── grafana-deployment.yml
│   └── 10-kubernetes-dashboard/
│       ├── README.md
│       ├── dashboard-deployment.yml
│       ├── serviceaccount.yml
│       ├── clusterrolebinding.yml
│       └── admin-user.yml
│   └── 11-crds/
│       ├── README.md
│       ├── website-crd.yaml
│       ├── my-website.yaml
│       └── websites.yaml
│   └── 12-helm/
│       └── README.md
│   └── 13-kustomize/
│       └── README.md
├── nginx/ (example YAML files)
├── mysql/ (example YAML files)
└── apache/ (example YAML files)
```

---

**Happy Learning! 🚀**
