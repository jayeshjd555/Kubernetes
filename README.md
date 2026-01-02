# Kubernetes Notes

## Table of Contents

1. [Introduction & Overview of Kubernetes Concepts](#introduction--overview-of-kubernetes-concepts)
2. [Kubernetes History](#kubernetes-history)
3. [Why Should We Learn Kubernetes?](#why-should-we-learn-kubernetes)
4. [Monolithic vs Microservices Architecture](#monolithic-vs-microservices-architecture)
5. [Kubernetes Architecture](#kubernetes-architecture)
6. [Kubernetes Setup](#kubernetes-setup)
7. [Kubernetes Concepts](#kubernetes-concepts)
   - [Kubernetes Objects and its Management](#kubernetes-objects-and-its-management)
   - [Field Selectors](#field-selectors)
   - [Namespaces](#namespaces)
   - [Pods](#pods)
   - [Labels and Selectors](#labels-and-selectors)
   - [Annotations](#annotations)
   - [ReplicaSet](#replicaset)
   - [Deployments](#deployments)
   - [StatefulSet](#statefulset)
   - [DaemonSet](#daemonset)
   - [Jobs](#jobs)
   - [CronJobs](#cronjobs)
   - [Storage](#storage)
     - [StorageClass](#storageclass)
     - [PersistentVolume](#persistentvolume-pv)
     - [PersistentVolumeClaim](#persistentvolumeclaim-pvc)
   - [Services](#services)
   - [Ingress](#ingress)
   - [ConfigMap and Secrets](#configmap-and-secrets)
    - [ConfigMap](#configmap)
    - [Secrets](#secrets)
  - [Resource Quotas and Limits](#resource-quotas-and-limits)
  - [Probes](#probes)
  - [Autoscaling](#autoscaling)
    - [HPA - Horizontal Pod Autoscaler](#hpa---horizontal-pod-autoscaler)
    - [VPA - Vertical Pod Autoscaler](#vpa---vertical-pod-autoscaler)
    - [KEDA - Kubernetes Event-Driven Autoscaling](#keda---kubernetes-event-driven-autoscaling)
    - [Cluster Autoscaler](#cluster-autoscaler)
    - [Karpenter](#karpenter)
  - [Taints and Tolerations](#taints-and-tolerations)

---

<details>
<summary><h2>Introduction & Overview of Kubernetes Concepts</h2></summary>

## Introduction & Overview of Kubernetes Concepts

### What is Kubernetes?

**Kubernetes** (often abbreviated as **K8s**) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications.

**Simple Explanation:**
Kubernetes is like an **autopilot system** for your containerized applications. Just as an autopilot manages a plane's flight, Kubernetes manages your applications' lifecycle, ensuring they run smoothly, scale when needed, and recover from failures automatically.

**Key Characteristics:**
- **Container Orchestration:** Manages multiple containers across multiple hosts
- **Automated Operations:** Handles deployment, scaling, and updates automatically
- **Self-Healing:** Automatically recovers from failures
- **Service Discovery:** Automatically manages networking and connectivity
- **Load Balancing:** Distributes traffic efficiently
- **Declarative Configuration:** You describe what you want, Kubernetes makes it happen

### Kubernetes in Simple Terms

**Think of Kubernetes as:**

**1. An Orchestra Conductor**
- A conductor coordinates all musicians (containers)
- Ensures everyone plays in harmony
- Manages the performance (application lifecycle)

**2. A Smart Manager**
- Ensures your applications are running, healthy, and accessible
- Handles all the operational tasks
- Makes decisions automatically

**3. An Autopilot System**
- Takes care of routine operations
- Handles unexpected situations
- Ensures smooth operation

### Key Concepts Explained

#### Container Orchestration

**What it means:**
- Manages multiple containers across multiple hosts
- Coordinates container lifecycle
- Handles container placement and scheduling

**Why it matters:**
- Without orchestration: Manual management of each container
- With orchestration: Automatic management of all containers

#### Automated Deployment

**What it means:**
- Deploys applications automatically
- Updates applications with zero downtime
- Handles rollbacks if needed

**Benefits:**
- Faster deployments
- Consistent deployments
- Reduced human error

#### Scaling

**What it means:**
- Automatically scales applications up or down based on demand
- Adds or removes containers as needed
- Maintains desired number of replicas

**Example:**
- Low traffic: 2 containers running
- High traffic: 10 containers running
- Kubernetes adjusts automatically

#### Self-Healing

**What it means:**
- Automatically restarts failed containers
- Replaces containers that crash
- Reschedules containers when nodes fail

**How it works:**
1. Container fails → Kubernetes detects it
2. Kubernetes restarts the container
3. If restart fails → Replaces with new container
4. If node fails → Reschedules on another node

#### Service Discovery

**What it means:**
- Automatically assigns IP addresses to containers
- Provides DNS names for services
- Enables containers to find each other

**Benefits:**
- No hardcoded IPs
- Automatic networking
- Easy service communication

#### Load Balancing

**What it means:**
- Distributes traffic across multiple containers
- Ensures even distribution
- Handles traffic spikes

**Benefits:**
- Better performance
- High availability
- Efficient resource usage

#### Rolling Updates

**What it means:**
- Updates applications with zero downtime
- Updates containers one at a time
- Maintains service availability during updates

**Benefits:**
- No service interruption
- Safe updates
- Easy rollback

### Without Kubernetes vs With Kubernetes

**Without Kubernetes:**
- ❌ **Manual Management:** You manage each container manually
- ❌ **Manual Scaling:** You add/remove containers manually
- ❌ **Manual Recovery:** You fix failures manually
- ❌ **Manual Networking:** You configure networking manually
- ❌ **Manual Updates:** You update applications manually
- ❌ **Time Consuming:** Lots of operational overhead

**With Kubernetes:**
- ✅ **Automatic Management:** Kubernetes manages everything
- ✅ **Automatic Scaling:** Scales based on demand
- ✅ **Automatic Recovery:** Self-healing capabilities
- ✅ **Automatic Networking:** Service discovery and load balancing
- ✅ **Automatic Updates:** Rolling updates with zero downtime
- ✅ **Time Saving:** Focus on application development

### Kubernetes Abbreviation: K8s

**Why K8s?**
- **K** + **8 letters** + **s** = **K8s**
- **K**ubernete**s** = K + 8 letters + s
- Easier to type and say
- Common abbreviation in the industry

### Key Takeaways

1. **Kubernetes automates container management** - Reduces operational overhead
2. **Self-healing capabilities** - Automatically recovers from failures
3. **Automatic scaling** - Handles traffic changes automatically
4. **Service discovery** - Automatic networking and connectivity
5. **Declarative model** - Describe desired state, Kubernetes makes it happen
6. **Industry standard** - Widely adopted and supported
7. **Cloud-agnostic** - Works on any cloud or on-premises

</details>

---

<details>
<summary><h2>Kubernetes History</h2></summary>

## Kubernetes History

### The Origin Story

**2013-2014: Google's Internal System**
- Kubernetes was inspired by **Borg**, Google's internal container orchestration system
- Google had been running containers at scale for over a decade
- They decided to open-source their container orchestration knowledge

**2014: Kubernetes is Born**
- **June 2014:** Kubernetes was announced by Google at DockerCon
- Initially developed by Google engineers
- Written in Go language
- Designed to work with Docker containers

**2015: Cloud Native Computing Foundation (CNCF)**
- **July 2015:** Kubernetes v1.0 was released
- **July 2015:** Google donated Kubernetes to the **Cloud Native Computing Foundation (CNCF)**
- CNCF was founded to advance container technology
- Kubernetes became the first CNCF project

**2016-2017: Rapid Growth**
- Major cloud providers adopted Kubernetes
- AWS, Azure, and GCP all launched managed Kubernetes services
- Community grew rapidly
- Many companies contributed to the project

**2018: Industry Standard**
- Kubernetes became the de facto standard for container orchestration
- **March 2018:** Kubernetes won the container orchestration war
- Docker Swarm and other competitors faded
- Enterprise adoption increased significantly

**2019-Present: Maturity and Innovation**
- Kubernetes reached maturity with stable releases
- New features: Operators, Service Mesh integration, Serverless
- Kubernetes 1.20+ with enhanced security and performance
- Widespread enterprise adoption

### Key Milestones

| Year | Milestone |
|------|-----------|
| **2014** | Kubernetes announced by Google |
| **2015** | v1.0 released, donated to CNCF |
| **2016** | Major cloud providers launch managed K8s |
| **2017** | Kubernetes becomes mainstream |
| **2018** | Industry standard established |
| **2019+** | Maturity, innovation, enterprise adoption |

### Why "Kubernetes"?

- **Greek origin:** "Kubernetes" means "helmsman" or "pilot" (someone who steers a ship)
- **Abbreviation:** Often called **K8s** (K + 8 letters + s)
- **Symbolism:** Represents steering/guiding containerized applications

</details>

---

<details>
<summary><h2>Why Should We Learn Kubernetes?</h2></summary>

## Why Should We Learn Kubernetes?

Kubernetes has become the de facto standard for container orchestration. Learning Kubernetes opens up numerous opportunities and provides essential skills for modern software development and operations.

### 1. Industry Standard

**What it means:**
Kubernetes is the **industry standard** for container orchestration. It's the most widely adopted solution for managing containerized applications.

**Key Facts:**
- **83% of organizations** running containers use Kubernetes (CNCF survey)
- **De facto standard** - Most common choice for container orchestration
- **Widespread adoption** - Used across industries and company sizes
- **Future-proof** - Skills will remain relevant for years to come

**Why it matters:**
- Learning Kubernetes means learning the standard
- Skills are transferable across companies
- Industry recognition and certification opportunities
- Continuous innovation and updates

### 2. Career Opportunities

**High Demand:**
- Kubernetes skills are in **extremely high demand**
- One of the fastest-growing skills in tech
- Appears in most DevOps/Cloud job descriptions

**Career Benefits:**
- **High Salaries:** Kubernetes engineers command premium salaries
- **Career Growth:** Opens doors to senior roles
- **Versatility:** Skills transfer across companies and industries
- **Multiple Paths:** DevOps, SRE, Cloud Engineering, Platform Engineering

**Job Market:**
- DevOps Engineers with K8s experience earn 20-30% more
- SRE roles require Kubernetes knowledge
- Cloud Engineers need Kubernetes expertise
- Growing demand in all tech sectors

### 3. Scalability and Reliability

**Auto-Scaling:**
- Automatically scales applications based on demand
- Handles traffic spikes without manual intervention
- Optimizes resource usage

**High Availability:**
- Ensures applications are always available
- Distributes workloads across multiple nodes
- Handles node failures gracefully

**Self-Healing:**
- Automatically recovers from failures
- Restarts failed containers
- Replaces unhealthy pods

**Zero Downtime:**
- Rolling updates with no service interruption
- Seamless deployments
- Easy rollbacks if needed

**Real-World Impact:**
- Applications handle traffic spikes automatically
- Reduced downtime and improved reliability
- Better user experience
- Lower operational costs

### 4. Cloud-Native Development

**Cloud Agnostic:**
- Works on **AWS, Azure, GCP**, and on-premises
- Same skills across different clouds
- Avoid vendor lock-in
- Portable applications

**Microservices Support:**
- Perfect for microservices architecture
- Manages multiple services efficiently
- Service discovery and load balancing
- Independent scaling per service

**Modern Applications:**
- Essential for modern, cloud-native applications
- Supports modern development practices
- CI/CD integration
- DevOps workflows

**Benefits:**
- Build once, run anywhere
- Consistent environments
- Modern development practices
- Faster time to market

### 5. Cost Efficiency

**Resource Optimization:**
- Efficiently uses compute resources
- Better resource utilization
- Reduces waste

**Multi-Tenancy:**
- Run multiple applications on same infrastructure
- Share resources efficiently
- Lower infrastructure costs

**Cost Reduction:**
- Better resource utilization = lower costs
- Automatic scaling = pay for what you use
- Reduced operational overhead
- Optimized infrastructure spending

**ROI:**
- Companies report 30-50% infrastructure cost reduction
- Reduced operational overhead
- Better resource utilization
- Lower total cost of ownership

### 6. Developer Productivity

**Declarative Configuration:**
- Define desired state, Kubernetes makes it happen
- Version-controlled configurations
- Infrastructure as code

**Automation:**
- Reduces manual operations
- Automates routine tasks
- Focus on development, not operations

**Consistency:**
- Same environment from dev to production
- Reproducible deployments
- Fewer environment-related bugs

**Faster Deployments:**
- Deploy applications faster and more reliably
- Automated deployment pipelines
- Reduced deployment time

**Developer Benefits:**
- More time for coding
- Less time on operations
- Faster iteration cycles
- Better developer experience

### 7. Ecosystem and Community

**Large Community:**
- Massive open-source community
- Active contributors worldwide
- Strong support network

**Rich Ecosystem:**
- Thousands of tools and extensions
- Helm charts for easy deployments
- Operators for complex applications
- Service meshes (Istio, Linkerd)

**Continuous Innovation:**
- Regular updates and new features
- Active development
- Community-driven improvements

**Extensive Documentation:**
- Great learning resources available
- Official documentation
- Community tutorials and guides
- Certification programs

**Learning Resources:**
- Official Kubernetes documentation
- Community tutorials and courses
- Certification programs (CKA, CKAD, CKS)
- Active forums and communities

### 8. Enterprise Adoption

**Fortune 500 Companies:**
- Used by most Fortune 500 companies
- Enterprise-grade features
- Production-ready at scale

**Startups:**
- Also adopted by startups for scalability
- Cost-effective for growing companies
- Flexible and scalable

**Government:**
- Used by government organizations
- Security and compliance features
- Reliable and stable

**Global Adoption:**
- Worldwide adoption across industries
- Cross-industry applicability
- International community

**Adoption Statistics:**
- 83% of organizations using containers use Kubernetes
- 78% of enterprises use Kubernetes in production
- Growing adoption across all industries
- Increasing investment in Kubernetes

### Who Should Learn Kubernetes?

**Essential For:**
- ✅ **DevOps Engineers** - Core skill for modern DevOps
- ✅ **Site Reliability Engineers (SRE)** - Essential for reliability
- ✅ **Cloud Engineers** - Required for cloud-native development
- ✅ **Platform Engineers** - Building internal platforms

**Highly Beneficial For:**
- ✅ **Software Engineers** - For deploying and managing applications
- ✅ **System Administrators** - Modern infrastructure management
- ✅ **Backend Engineers** - Understanding deployment and operations
- ✅ **Full-Stack Developers** - Complete application lifecycle

**Valuable For:**
- ✅ **QA Engineers** - Understanding test environments
- ✅ **Technical Leads** - Architecture and design decisions
- ✅ **IT Managers** - Strategic technology decisions
- ✅ **Anyone in Tech** - Valuable skill for career growth

### Key Takeaways

1. **Industry standard** - Most widely adopted container orchestration
2. **Career opportunities** - High demand and good salaries
3. **Scalability and reliability** - Automatic scaling and self-healing
4. **Cloud-native** - Essential for modern applications
5. **Cost efficient** - Better resource utilization
6. **Developer productivity** - Automation and consistency
7. **Rich ecosystem** - Large community and tools
8. **Enterprise adoption** - Used by major companies worldwide

</details>

---

<details>
<summary><h2>Monolithic vs Microservices Architecture</h2></summary>

## Monolithic vs Microservices Architecture

Understanding the difference between monolithic and microservices architecture is crucial for understanding why Kubernetes is important. This section explains both architectures, their characteristics, and when to use each.

### What is Architecture?

**Architecture** refers to how an application is structured and organized. The architecture you choose affects:
- How you develop the application
- How you deploy the application
- How you scale the application
- How you maintain the application

**Two Main Approaches:**
1. **Monolithic Architecture** - Everything in one place
2. **Microservices Architecture** - Multiple independent services

### Monolithic Architecture

**What is it?**
A monolithic application is built as a **single, unified unit** where all components are tightly coupled and deployed together.

**Simple Analogy:**
Think of a monolithic application like a **large building**:
- Everything is in one structure
- All rooms (components) are connected
- You can't move one room without affecting others
- If one part fails, the whole building might be affected

**Characteristics:**
- **Single Codebase:** All code in one repository
- **Single Deployment:** Deploy everything together
- **Tight Coupling:** Components depend on each other
- **Shared Database:** Usually one database for everything
- **Single Technology Stack:** One programming language/framework

**How it Works:**
```
Monolithic Application
├── User Management Module
├── Order Processing Module
├── Payment Module
├── Inventory Module
└── All in one codebase, one deployment
```

**Advantages:**
✅ **Simple Development:** Easier to develop initially
✅ **Easy Testing:** Test everything together
✅ **Simple Deployment:** Deploy one unit
✅ **No Network Latency:** All components in same process
✅ **Easier Debugging:** Everything in one place
✅ **Transaction Management:** Easier to manage transactions

**Disadvantages:**
❌ **Scaling Challenges:** Must scale entire application
❌ **Technology Lock-in:** Stuck with one technology
❌ **Single Point of Failure:** One bug can affect everything
❌ **Team Collaboration:** Difficult for large teams
❌ **Deployment Risk:** One deployment affects everything
❌ **Long Startup Time:** Large application takes time to start

**When to Use:**
- Small applications
- Small teams
- Simple requirements
- Rapid prototyping
- MVP (Minimum Viable Product)
- When you need to move fast initially

### Microservices Architecture

**What is it?**
A microservices architecture is an approach where an application is built as a **collection of small, independent services** that communicate over well-defined APIs.

**Simple Analogy:**
Think of microservices like a **shopping mall**:
- Multiple independent stores (services)
- Each store operates independently
- Stores communicate through common areas (APIs)
- One store can close without affecting others
- Each store can have different management (technology)

**Characteristics:**
- **Multiple Services:** Each service is independent
- **Independent Deployment:** Deploy services separately
- **Loose Coupling:** Services communicate via APIs
- **Database per Service:** Each service has its own database
- **Technology Diversity:** Different services can use different technologies

**How it Works:**
```
Microservices Application
├── User Service (Node.js, MongoDB)
├── Order Service (Java, PostgreSQL)
├── Payment Service (Python, Redis)
├── Inventory Service (Go, MySQL)
└── Each service is independent
```

**Advantages:**
✅ **Independent Scaling:** Scale each service independently
✅ **Technology Flexibility:** Use best technology for each service
✅ **Fault Isolation:** One service failure doesn't affect others
✅ **Team Autonomy:** Teams can work independently
✅ **Faster Deployment:** Deploy one service at a time
✅ **Better Resource Utilization:** Allocate resources per service

**Disadvantages:**
❌ **More Complexity:** More moving parts to manage
❌ **Network Latency:** Services communicate over network
❌ **Distributed Transactions:** Harder to manage transactions
❌ **Operational Overhead:** More services to monitor and manage
❌ **Testing Complexity:** Need to test service interactions
❌ **Data Consistency:** Harder to maintain consistency

**When to Use:**
- Large applications
- Large teams
- Scalability needs
- Technology diversity requirements
- Cloud-native applications
- When you need independent scaling

### Comparison Table

| Aspect | Monolithic | Microservices |
|--------|-----------|---------------|
| **Structure** | Single unified application | Collection of independent services |
| **Deployment** | Single deployment unit | Multiple independent deployments |
| **Scaling** | Scale entire application | Scale individual services |
| **Technology** | Single technology stack | Multiple technologies possible |
| **Database** | Usually single database | Database per service |
| **Complexity** | Simple initially | Complex from start |
| **Fault Tolerance** | Single point of failure | Isolated failures |
| **Team Size** | Works well for small teams | Better for large teams |
| **Development Speed** | Faster initially | Slower initially, faster later |
| **Testing** | Easier to test | More complex testing |
| **Deployment** | All or nothing | Independent deployments |
| **Resource Usage** | Fixed resources | Flexible resource allocation |

### When to Choose Which?

**Choose Monolithic When:**
- ✅ Building a small application
- ✅ Small team (1-5 developers)
- ✅ Simple requirements
- ✅ Need to move fast (MVP)
- ✅ Limited scalability needs
- ✅ Single technology stack is sufficient

**Choose Microservices When:**
- ✅ Building a large application
- ✅ Large team (10+ developers)
- ✅ Need independent scaling
- ✅ Need technology diversity
- ✅ Cloud-native requirements
- ✅ High availability needs
- ✅ Different services have different requirements

**Migration Path:**
Many companies start with monolithic and migrate to microservices:
1. **Start Monolithic:** Faster initial development
2. **Identify Boundaries:** Find natural service boundaries
3. **Extract Services:** Gradually extract services
4. **Full Microservices:** Complete migration over time

### Kubernetes and Microservices

**Why Kubernetes is Perfect for Microservices:**

**1. Service Management:**
- Manages multiple services easily
- Each service runs in its own Pods
- Independent lifecycle management

**2. Independent Scaling:**
- Scale each service independently
- Different replica counts per service
- Auto-scaling per service

**3. Service Discovery:**
- Automatic service discovery
- DNS-based service names
- Easy service communication

**4. Load Balancing:**
- Load balances across service instances
- Distributes traffic evenly
- Handles service failures

**5. Health Monitoring:**
- Health checks per service
- Automatic restart of failed services
- Rolling updates per service

**Example Architecture:**
```
Kubernetes Cluster
├── User Service
│   ├── Pod 1 (User Service)
│   ├── Pod 2 (User Service)
│   └── Pod 3 (User Service)
├── Order Service
│   ├── Pod 1 (Order Service)
│   ├── Pod 2 (Order Service)
│   ├── Pod 3 (Order Service)
│   ├── Pod 4 (Order Service)
│   └── Pod 5 (Order Service)
├── Payment Service
│   ├── Pod 1 (Payment Service)
│   └── Pod 2 (Payment Service)
└── Inventory Service
    ├── Pod 1 (Inventory Service)
    ├── Pod 2 (Inventory Service)
    ├── Pod 3 (Inventory Service)
    └── Pod 4 (Inventory Service)
```

**Benefits of Kubernetes for Microservices:**
- ✅ **Orchestration:** Manages all services automatically
- ✅ **Networking:** Handles service-to-service communication
- ✅ **Scaling:** Independent scaling per service
- ✅ **Updates:** Rolling updates per service
- ✅ **Monitoring:** Health checks and monitoring
- ✅ **Resource Management:** Efficient resource allocation

### Key Takeaways

1. **Monolithic** is simpler for small applications and teams
2. **Microservices** are better for large, complex applications
3. **Kubernetes** makes managing microservices much easier
4. **Choose architecture** based on your specific needs and scale
5. **Start simple** - You can migrate from monolithic to microservices
6. **Kubernetes** provides the orchestration needed for microservices
7. **Both architectures** have their place - choose wisely

</details>

---

<details>
<summary><h2>Kubernetes Architecture</h2></summary>

## Kubernetes Architecture

Kubernetes follows a **master-worker (control plane-worker node) architecture** where the control plane manages the cluster and worker nodes run the applications.

### What is Kubernetes Architecture?

**Simple Explanation:**
Kubernetes architecture is like a **company structure**:
- **Control Plane (Management):** Like the management team that makes decisions
- **Worker Nodes (Employees):** Like workers who do the actual work
- **Communication:** Management gives instructions, workers report back

**Key Concept:**
Kubernetes separates **management** (control plane) from **execution** (worker nodes). This separation allows:
- Scalability: Add more worker nodes without changing management
- Reliability: Control plane can be highly available
- Flexibility: Different types of worker nodes for different workloads

### Architecture Overview

A Kubernetes cluster consists of two main parts:

**1. Control Plane (Master Nodes):**
- The **brain** of the cluster
- Makes all decisions about the cluster
- Manages cluster state
- Coordinates all activities

**2. Worker Nodes:**
- The **workers** that run your applications
- Execute the work assigned by control plane
- Report status back to control plane
- Can scale independently

**Additional Components:**
- **etcd:** Distributed key-value store for cluster state
- **Networking:** Pod network for communication between components

### Architecture Diagram

```mermaid
graph TB
    subgraph "Control Plane (Master Node)"
        API[API Server<br/>Entry Point for all Operations]
        Scheduler[Scheduler<br/>Assigns Pods to Nodes]
        CM[Controller Manager<br/>Manages Controllers]
        etcd[etcd<br/>Cluster State Store]
        CC[Cloud Controller<br/>Cloud Provider Integration]
    end
    
    subgraph "Worker Node 1"
        Kubelet1[kubelet<br/>Node Agent]
        KubeProxy1[kube-proxy<br/>Network Proxy]
        Runtime1[Container Runtime<br/>Docker/containerd/CRI-O]
        Pod1[Pods<br/>Application Containers]
    end
    
    subgraph "Worker Node 2"
        Kubelet2[kubelet<br/>Node Agent]
        KubeProxy2[kube-proxy<br/>Network Proxy]
        Runtime2[Container Runtime<br/>Docker/containerd/CRI-O]
        Pod2[Pods<br/>Application Containers]
    end
    
    subgraph "Worker Node N"
        KubeletN[kubelet<br/>Node Agent]
        KubeProxyN[kube-proxy<br/>Network Proxy]
        RuntimeN[Container Runtime<br/>Docker/containerd/CRI-O]
        PodN[Pods<br/>Application Containers]
    end
    
    API --> etcd
    API --> Scheduler
    API --> CM
    API --> CC
    
    API --> Kubelet1
    API --> Kubelet2
    API --> KubeletN
    
    Kubelet1 --> Runtime1
    Kubelet1 --> Pod1
    KubeProxy1 --> Pod1
    
    Kubelet2 --> Runtime2
    Kubelet2 --> Pod2
    KubeProxy2 --> Pod2
    
    KubeletN --> RuntimeN
    KubeletN --> PodN
    KubeProxyN --> PodN
    
    style API fill:#326ce5,color:#fff
    style Scheduler fill:#326ce5,color:#fff
    style CM fill:#326ce5,color:#fff
    style etcd fill:#326ce5,color:#fff
    style CC fill:#326ce5,color:#fff
    style Kubelet1 fill:#00d4aa,color:#fff
    style Kubelet2 fill:#00d4aa,color:#fff
    style KubeletN fill:#00d4aa,color:#fff
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
    style PodN fill:#f4a261,color:#000
```

### Control Plane Components

The **control plane** (formerly called master node) is the **brain of the Kubernetes cluster**. It makes global decisions about the cluster and responds to cluster events.

**What it does:**
- Makes decisions about the cluster
- Maintains desired state
- Responds to cluster events
- Manages cluster resources
- Coordinates worker nodes

**Key Characteristics:**
- Can run on dedicated machines or on worker nodes
- Should be highly available in production
- All components communicate through API Server
- Stateless design allows horizontal scaling

#### 1. API Server (kube-apiserver)

**What it is:**
The **API Server** is the **front-end for the Kubernetes control plane**. It's the central management point for the entire cluster.

**Simple Analogy:**
Think of API Server as a **reception desk**:
- All requests come through here
- Validates and processes requests
- Routes requests to appropriate components
- Returns responses to clients

**Responsibilities:**
- **Exposes Kubernetes API:** RESTful API for all operations
- **Validates Requests:** Ensures requests are valid
- **Authenticates & Authorizes:** Security layer for requests
- **Updates etcd:** Stores cluster state in etcd
- **Serves as Gateway:** Only component that talks to etcd directly
- **Watches & Notifies:** Watches for changes and notifies components

**Key Features:**
- **RESTful API:** Standard HTTP/JSON API
- **Horizontal Scaling:** Can run multiple instances
- **Stateless:** Can be load balanced
- **Secure:** Authentication and authorization built-in
- **Extensible:** Supports custom resources and extensions

**How it Works:**
```
User/Component → API Server → Validates → etcd → Response
```

**Example:**
```bash
# All kubectl commands go through API Server
kubectl get pods
# → kubectl → API Server → etcd → Response

kubectl create deployment
# → kubectl → API Server → Validates → etcd → Scheduler notified
```

**Important Notes:**
- ⚠️ **All communication** goes through API Server
- ⚠️ **Single point of entry** for all operations
- ⚠️ **Should be highly available** in production
- ✅ **Can be scaled** horizontally for performance

#### 2. etcd

**What it is:**
**etcd** is a **distributed, consistent key-value store** used as Kubernetes' backing store. It's the **database** of the Kubernetes cluster.

**Simple Analogy:**
Think of etcd as a **filing cabinet**:
- Stores all important information
- Only API Server can access it directly
- Contains the current state of everything
- If lost, you lose all records

**Responsibilities:**
- **Stores Cluster Data:** All configurations, state, metadata
- **Provides Watch Functionality:** Real-time change notifications
- **Ensures Consistency:** Distributed consensus algorithm
- **Source of Truth:** Single source of truth for cluster state

**Key Features:**
- **Highly Available:** Can run in HA mode (3+ nodes)
- **Persistent Storage:** Data survives restarts
- **Fast Reads/Writes:** Optimized for performance
- **Watch API:** Real-time updates for components
- **Consistent:** Strong consistency guarantees

**What it Stores:**
- Pod definitions and states
- Service definitions
- Deployment configurations
- Node information
- Secrets and ConfigMaps
- All cluster metadata

**Important:**
- ⚠️ **Backup etcd regularly** - Contains all cluster state
- ⚠️ **etcd is the source of truth** - If lost, cluster state is lost
- ⚠️ **Only API Server** talks to etcd directly
- ✅ **Highly available** setup recommended for production

#### 3. Scheduler (kube-scheduler)

**What it is:**
The **Scheduler** is a control plane component that watches for newly created Pods with no assigned node, and **selects a node for them to run on**.

**Simple Analogy:**
Think of Scheduler as a **job assignment manager**:
- New tasks (Pods) need to be assigned
- Evaluates all available workers (Nodes)
- Chooses the best worker for each task
- Considers skills, availability, and preferences

**Responsibilities:**
- **Assigns Pods to Nodes:** Decides where Pods run
- **Considers Resource Requirements:** CPU, memory, storage needs
- **Considers Constraints:** Hardware/software requirements
- **Considers Affinity Rules:** Pod/node affinity and anti-affinity
- **Considers Data Locality:** Places Pods near their data
- **Load Balancing:** Distributes Pods across nodes

**Scheduling Process:**
The scheduler uses a two-phase approach:

**Phase 1: Filtering**
- Filters out nodes that can't run the Pod
- Checks resource availability
- Checks node constraints
- Checks taints and tolerations

**Phase 2: Scoring**
- Scores remaining nodes
- Considers resource balance
- Considers affinity rules
- Considers data locality
- Selects highest-scoring node

**Detailed Process:**
```
1. Filter nodes (find nodes that can run the pod)
   ↓
2. Score nodes (rank nodes by preference)
   ↓
3. Select best node (highest score)
   ↓
4. Bind pod to node (assign pod to selected node)
```

**Example:**
```
New Pod Created (needs 2 CPU, 4GB RAM)
   ↓
Scheduler evaluates all nodes
   ↓
Filters: Node 1 (has resources), Node 2 (has resources), Node 3 (insufficient)
   ↓
Scores: Node 1 (score: 85), Node 2 (score: 92)
   ↓
Selects: Node 2 (highest score)
   ↓
Assigns Pod to Node 2
```

**Key Features:**
- **Intelligent Placement:** Considers multiple factors
- **Load Balancing:** Distributes Pods evenly
- **Extensible:** Can add custom scheduling logic
- **Efficient:** Fast scheduling decisions

#### 4. Controller Manager (kube-controller-manager)

**What it is:**
The **Controller Manager** runs controller processes that **regulate the state of the cluster**. It ensures the actual state matches the desired state.

**Simple Analogy:**
Think of Controller Manager as a **quality control supervisor**:
- Monitors what should be (desired state)
- Checks what actually is (actual state)
- Takes action when they don't match
- Continuously ensures everything is correct

**Responsibilities:**
- **Watches Cluster State:** Monitors desired vs actual state
- **Takes Corrective Action:** Fixes discrepancies
- **Manages Controllers:** Runs multiple controllers
- **Reconciles State:** Continuously ensures consistency

**Controllers:**
The Controller Manager runs multiple controllers:

**Workload Controllers:**
- **Replication Controller:** Maintains correct number of pod replicas
- **Deployment Controller:** Manages deployments and rolling updates
- **StatefulSet Controller:** Manages stateful applications
- **DaemonSet Controller:** Ensures pods run on all/some nodes
- **Job Controller:** Manages job completion
- **CronJob Controller:** Manages scheduled jobs

**Cluster Controllers:**
- **Node Controller:** Monitors node health and availability
- **Service Controller:** Manages cloud load balancers
- **Endpoint Controller:** Populates endpoint objects for Services
- **Namespace Controller:** Manages namespace lifecycle
- **ResourceQuota Controller:** Enforces resource quotas

**How it Works:**
The Controller Manager follows a **reconciliation loop**:

```
1. Watch desired state (from API Server/etcd)
   ↓
2. Check actual state (from API Server/etcd)
   ↓
3. Compare desired vs actual
   ↓
4. If different → Take corrective action
   ↓
5. Repeat continuously
```

**Example:**
```
Desired: 3 replicas of nginx
Actual: 2 replicas running
   ↓
Controller detects mismatch
   ↓
Creates 1 new Pod
   ↓
Actual: 3 replicas running ✅
```

**Key Features:**
- **Continuous Monitoring:** Always watching cluster state
- **Automatic Correction:** Fixes issues automatically
- **Self-Healing:** Maintains desired state
- **Multiple Controllers:** Manages different aspects

#### 5. Cloud Controller Manager (cloud-controller-manager)

**What it is:** Links your cluster into your cloud provider's API.

**Responsibilities:**
- Node controller (cloud-specific)
- Route controller (cloud-specific)
- Service controller (load balancers)
- Volume controller (storage)

**Note:** Only runs when cloud provider integration is enabled.

### Worker Node Components

**Worker nodes** (formerly called minions) run your **application workloads**. Each node must have the components below.

**What Worker Nodes Do:**
- Execute application workloads (Pods)
- Report status to control plane
- Run container runtime
- Handle networking for Pods

**Key Characteristics:**
- Can scale independently (add/remove nodes)
- Each node is independent
- Can have different resources (CPU, memory)
- Can be in different zones/regions

#### 1. kubelet

**What it is:**
**kubelet** is an **agent that runs on each node** in the cluster. It's the primary node agent that communicates with the control plane.

**Simple Analogy:**
Think of kubelet as a **foreman on a construction site**:
- Receives instructions from management (API Server)
- Manages workers (containers) on the site
- Reports progress back to management
- Ensures work is done correctly

**Responsibilities:**
- **Communicates with API Server:** Receives instructions and reports status
- **Manages Pods:** Creates, updates, and deletes Pods
- **Manages Containers:** Ensures containers are running
- **Reports Status:** Reports node and pod status to API Server
- **Executes Health Checks:** Runs liveness and readiness probes
- **Mounts Volumes:** Attaches storage volumes to Pods
- **Downloads Secrets:** Retrieves secrets for Pods
- **Registers Node:** Registers the node with the cluster

**Key Features:**
- **Node Registration:** Registers node with API Server
- **Health Monitoring:** Monitors pod and container health
- **Auto-Recovery:** Restarts failed containers
- **Resource Reporting:** Reports CPU, memory, disk usage
- **Volume Management:** Manages volume mounts
- **Secret Management:** Handles secret retrieval

**Communication Flow:**
```
API Server → kubelet → Container Runtime → Containers
   ↑                                              ↓
   └────────── Status Reports ────────────────────┘
```

**How it Works:**
1. **Receives Pod Spec:** Gets Pod specification from API Server
2. **Creates Pod:** Instructs Container Runtime to create containers
3. **Monitors Health:** Continuously monitors Pod health
4. **Reports Status:** Sends status updates to API Server
5. **Takes Action:** Restarts containers if they fail

**Example:**
```
API Server: "Create Pod with nginx container"
   ↓
kubelet: "Received, creating Pod"
   ↓
kubelet → Container Runtime: "Pull nginx image and start container"
   ↓
Container Runtime: "Container running"
   ↓
kubelet → API Server: "Pod is Running"
```

#### 2. kube-proxy

**What it is:**
**kube-proxy** is a **network proxy that runs on each node** in your cluster. It maintains network rules that allow communication to Pods from inside or outside the cluster.

**Simple Analogy:**
Think of kube-proxy as a **traffic director**:
- Routes traffic to the right destination
- Maintains routing rules
- Load balances traffic
- Handles network abstraction

**Responsibilities:**
- **Maintains Network Rules:** Sets up iptables/IPVS rules
- **Enables Service Abstraction:** Makes Services work
- **Load Balances Traffic:** Distributes traffic to Pods
- **Implements Service Types:** ClusterIP, NodePort, LoadBalancer
- **Service Discovery:** Enables Pod-to-Pod communication

**Why We Need It:**
- Pods have dynamic IPs (change when recreated)
- Services provide stable endpoints
- kube-proxy routes Service traffic to Pods
- Handles load balancing across Pods

**Modes:**
kube-proxy can run in different modes:

**1. iptables Mode (Default):**
- Uses Linux iptables rules
- Better performance than userspace
- No user-space switching
- Most common in production

**2. IPVS Mode:**
- Uses IPVS (IP Virtual Server)
- Better performance for large clusters
- More load balancing algorithms
- Recommended for high-traffic clusters

**3. userspace Mode (Legacy):**
- Proxy runs in userspace
- Older implementation
- Less efficient
- Not recommended for production

**How it Works:**
```
External Request → Service (ClusterIP: 10.96.0.1)
   ↓
kube-proxy (iptables rules)
   ↓
Routes to Pod IPs (10.244.1.5, 10.244.1.6, 10.244.1.7)
   ↓
Load balanced across Pods
```

**Example:**
```
Service: nginx-service (ClusterIP: 10.96.0.1:80)
   ↓
kube-proxy creates iptables rules:
   - 10.96.0.1:80 → 10.244.1.5:80 (Pod 1)
   - 10.96.0.1:80 → 10.244.1.6:80 (Pod 2)
   - 10.96.0.1:80 → 10.244.1.7:80 (Pod 3)
   ↓
Traffic to Service is load balanced across Pods
```

**Key Features:**
- **Automatic Updates:** Updates rules when Pods change
- **Load Balancing:** Distributes traffic evenly
- **Service Types:** Supports all Service types
- **High Performance:** Efficient routing

#### 3. Container Runtime

**What it is:**
The **Container Runtime** is software responsible for **running containers**. It's the component that actually executes containers on the node.

**Simple Analogy:**
Think of Container Runtime as a **factory machine**:
- Receives instructions (from kubelet)
- Pulls materials (container images)
- Produces products (running containers)
- Manages the production process

**Responsibilities:**
- **Pulling Images:** Downloads container images from registries
- **Starting Containers:** Creates and starts containers
- **Stopping Containers:** Stops and removes containers
- **Managing Lifecycle:** Handles container lifecycle
- **Container Isolation:** Provides isolation and security
- **Resource Management:** Manages container resources

**Supported Runtimes:**
Kubernetes supports multiple container runtimes through CRI:

**1. containerd:**
- Industry-standard container runtime
- Used by Docker Engine
- Lightweight and efficient
- Most common in production

**2. CRI-O:**
- Lightweight container runtime
- Designed specifically for Kubernetes
- OCI-compliant
- Good for resource-constrained environments

**3. Docker:**
- Via containerd (Docker Engine uses containerd)
- Familiar to many developers
- Widely used
- Being phased out in favor of containerd

**4. Mirantis Container Runtime:**
- Docker alternative
- Compatible with Docker
- Enterprise-focused

**Container Runtime Interface (CRI):**
**What is CRI?**
- **Standard interface** for container runtimes
- Allows Kubernetes to work with different runtimes
- Abstraction layer between kubelet and runtime

**Why CRI?**
- **Flexibility:** Can use different runtimes
- **Standardization:** Common interface for all runtimes
- **Innovation:** New runtimes can be added easily
- **Abstraction:** kubelet doesn't need to know runtime details

**How it Works:**
```
kubelet → CRI → Container Runtime → Containers
```

**Example:**
```
kubelet: "Create container with nginx:latest image"
   ↓
CRI Interface
   ↓
Container Runtime (containerd): "Pulling image, creating container"
   ↓
Container: "nginx container running"
   ↓
Container Runtime → kubelet: "Container started successfully"
```

**Key Features:**
- **OCI Compliant:** Follows Open Container Initiative standards
- **Secure:** Container isolation and security
- **Efficient:** Optimized for performance
- **Flexible:** Multiple runtime options

### How Components Work Together

#### Example: Deploying a Pod

```
1. User runs: kubectl create -f pod.yaml
   ↓
2. kubectl sends request to API Server
   ↓
3. API Server validates and stores in etcd
   ↓
4. Scheduler watches for unscheduled pods
   ↓
5. Scheduler selects node and updates pod spec
   ↓
6. API Server updates etcd
   ↓
7. kubelet on selected node watches for new pods
   ↓
8. kubelet instructs Container Runtime to create container
   ↓
9. Container Runtime pulls image and starts container
   ↓
10. kubelet reports pod status to API Server
    ↓
11. API Server updates etcd
```

### High Availability (HA) Architecture

For production, the control plane should be highly available:

```
Control Plane (HA Setup)
├── API Server (3+ instances, load balanced)
├── etcd (3+ instances, clustered)
├── Scheduler (3+ instances, leader election)
└── Controller Manager (3+ instances, leader election)
```

**Benefits:**
- No single point of failure
- Automatic failover
- Continuous availability

### Key Architecture Concepts

#### Declarative Model

- **You declare desired state** (YAML files)
- **Kubernetes makes it happen** (controllers reconcile)
- **Kubernetes maintains state** (self-healing)

#### API-Driven

- **Everything is an API call** to API Server
- **kubectl is just a client** - Makes API calls
- **Other tools can use API** - CI/CD, monitoring, etc.

#### Event-Driven

- **Components watch for changes** (etcd watch API)
- **Controllers react to events** (pod created, node failed)
- **Real-time reconciliation** (continuous monitoring)

### Architecture Best Practices

1. **Separate Control Plane and Worker Nodes**
   - Control plane nodes should be dedicated
   - Worker nodes can be shared

2. **High Availability**
   - Run multiple control plane nodes
   - Use load balancer for API Server
   - Cluster etcd for redundancy

3. **Resource Allocation**
   - Control plane needs sufficient resources
   - Worker nodes sized for workloads

4. **Security**
   - Secure API Server (TLS, authentication)
   - Secure etcd (encryption at rest)
   - Network policies for pod communication

5. **Monitoring**
   - Monitor control plane components
   - Monitor worker node health
   - Monitor etcd performance

</details>

---

<details>
<summary><h2>Kubernetes Setup</h2></summary>

## Kubernetes Setup

There are several ways to set up a Kubernetes cluster, each suited for different use cases. This section provides an overview of the main setup methods.

### Setup Methods Overview

| Method | Use Case | Complexity | Best For |
|--------|----------|------------|----------|
| **Minikube** | Local development | Low | Learning, local testing |
| **KIND** | Local development | Low | CI/CD, local testing |
| **kubeadm** | On-premises/Cloud | Medium | Production, custom setups |
| **Cloud Managed** | Production | Low | Production, enterprise |

---

### 1. Minikube

**What it is:** Tool that runs a single-node Kubernetes cluster inside a VM on your local machine.

**Characteristics:**
- ✅ Simple setup and installation
- ✅ Runs on Windows, macOS, and Linux
- ✅ Good for learning Kubernetes
- ✅ Supports multiple container runtimes (Docker, containerd, CRI-O)
- ⚠️ Single node only (not for production)
- ⚠️ Requires virtualization support

**When to Use:**
- Learning Kubernetes concepts
- Local development and testing
- Quick experimentation
- Testing Kubernetes features

**Installation:**
- Download from: https://minikube.sigs.k8s.io/docs/start/
- Supports multiple drivers (Docker, VirtualBox, Hyper-V, etc.)

**Basic Commands:**
```bash
minikube start
minikube stop
minikube delete
minikube status
```

---

### 2. KIND (Kubernetes in Docker)

**What it is:** Tool for running local Kubernetes clusters using Docker container "nodes".

**Characteristics:**
- ✅ Very fast startup
- ✅ Lightweight (uses Docker containers)
- ✅ Can run multiple clusters
- ✅ Great for CI/CD pipelines
- ✅ No VM required
- ⚠️ Limited to Docker runtime
- ⚠️ Not for production workloads

**When to Use:**
- CI/CD pipeline testing
- Local development
- Quick cluster testing
- Multi-cluster scenarios

**Installation:**
- Install via: `go install` or download binaries
- Requires Docker to be running

**Basic Commands:**
```bash
kind create cluster
kind delete cluster
kind get clusters
```

---

### 3. kubeadm

**What it is:** Tool that provides a simple way to create a Kubernetes cluster by following best practices.

**Characteristics:**
- ✅ Production-ready setup
- ✅ Full control over cluster configuration
- ✅ Can create multi-node clusters
- ✅ Works on any Linux machine
- ⚠️ Requires manual setup and configuration
- ⚠️ More complex than managed solutions
- ⚠️ You manage cluster lifecycle

**When to Use:**
- On-premises deployments
- Custom infrastructure requirements
- Learning cluster internals
- Production environments (when managed services aren't suitable)

**Installation:**
- Install kubeadm, kubelet, and kubectl on each node
- Initialize control plane node
- Join worker nodes to cluster

**Basic Commands:**
```bash
kubeadm init
kubeadm join
kubeadm reset
```

---

### 4. Cloud Managed Kubernetes Services

Cloud providers offer fully managed Kubernetes services that handle cluster setup, upgrades, and maintenance.

#### Amazon EKS (Elastic Kubernetes Service)

**What it is:** AWS managed Kubernetes service.

**Characteristics:**
- ✅ Fully managed control plane
- ✅ Integrates with AWS services (IAM, VPC, ELB)
- ✅ High availability
- ✅ Automatic updates
- ⚠️ Cost: Pay for control plane + worker nodes
- ⚠️ AWS-specific

**When to Use:**
- Production workloads on AWS
- Need AWS service integration
- Want managed control plane

**Setup:**
- Create cluster via AWS Console, CLI, or Terraform
- Configure worker nodes (EC2 or Fargate)
- Connect using kubectl

---

#### Azure AKS (Azure Kubernetes Service)

**What it is:** Azure managed Kubernetes service.

**Characteristics:**
- ✅ Fully managed control plane
- ✅ Integrates with Azure services (Active Directory, Azure Monitor)
- ✅ High availability
- ✅ Automatic scaling
- ⚠️ Cost: Pay for worker nodes (control plane free)
- ⚠️ Azure-specific

**When to Use:**
- Production workloads on Azure
- Need Azure service integration
- Enterprise Azure environments

**Setup:**
- Create cluster via Azure Portal, CLI, or ARM/Terraform
- Configure node pools
- Connect using kubectl

---

#### Google GKE (Google Kubernetes Engine)

**What it is:** Google Cloud managed Kubernetes service.

**Characteristics:**
- ✅ Fully managed (original Kubernetes creator)
- ✅ Integrates with GCP services
- ✅ Advanced features (Autopilot mode)
- ✅ High availability
- ⚠️ Cost: Pay for cluster resources
- ⚠️ GCP-specific

**When to Use:**
- Production workloads on GCP
- Need GCP service integration
- Want latest Kubernetes features

**Setup:**
- Create cluster via GCP Console, gcloud CLI, or Terraform
- Configure node pools
- Connect using kubectl

---

### Comparison Summary

| Feature | Minikube | KIND | kubeadm | Cloud Managed |
|---------|----------|------|---------|---------------|
| **Setup Time** | Minutes | Seconds | Hours | Minutes |
| **Cost** | Free | Free | Free (infrastructure) | Pay per use |
| **Production Ready** | ❌ No | ❌ No | ✅ Yes | ✅ Yes |
| **Multi-Node** | ❌ No | ✅ Yes | ✅ Yes | ✅ Yes |
| **Managed** | ❌ No | ❌ No | ❌ No | ✅ Yes |
| **Learning** | ✅ Excellent | ✅ Good | ✅ Good | ⚠️ Less control |
| **CI/CD** | ⚠️ Limited | ✅ Excellent | ⚠️ Complex | ✅ Good |

---

### Choosing the Right Setup Method

**For Learning:**
- Start with **Minikube** or **KIND** for local development

**For Development:**
- Use **Minikube**, **KIND**, or cloud managed service

**For CI/CD:**
- Use **KIND** for fast, lightweight testing

**For Production:**
- Use **Cloud Managed** services (EKS/AKS/GKE) for ease of management
- Use **kubeadm** for on-premises or custom requirements

**For On-Premises:**
- Use **kubeadm** for full control
- Consider managed solutions like Rancher, OpenShift

---

### Quick Start Recommendations

1. **Beginners:** Start with Minikube for hands-on learning
2. **Developers:** Use KIND for fast local development
3. **Production:** Use cloud managed services (EKS/AKS/GKE)
4. **Custom Needs:** Use kubeadm for full control

Each method has its place in the Kubernetes ecosystem, and you may use different methods for different purposes.

</details>

---

<details>
<summary><h2>Kubernetes Concepts</h2></summary>

## Kubernetes Concepts

This section covers fundamental Kubernetes concepts that you need to understand to work effectively with Kubernetes clusters.

### Kubernetes Objects and its Management

Kubernetes objects are persistent entities in the Kubernetes system that represent the state of your cluster. They describe what containerized applications are running, what resources they're using, and policies around how those applications behave.

#### What are Kubernetes Objects?

**Kubernetes Objects** are records of intent - you create objects to describe the desired state of your cluster. Kubernetes continuously works to ensure the actual state matches your desired state.

**Key Characteristics:**
- **Persistent:** Objects persist in etcd (cluster state store)
- **Declarative:** You describe desired state, Kubernetes makes it happen
- **Managed:** Kubernetes controllers manage object lifecycle
- **Namespaced or Cluster-scoped:** Objects belong to namespaces or cluster-wide

#### Object Spec and Status

Every Kubernetes object has two important fields:

1. **spec:** Describes the desired state
   - What you want the object to look like
   - Defined by you when creating the object

2. **status:** Describes the actual state
   - Current state of the object
   - Managed by Kubernetes

**Example:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:                    # Desired state
  containers:
  - name: nginx
    image: nginx:latest
status:                  # Actual state (managed by Kubernetes)
  phase: Running
  conditions: [...]
```

#### Common Kubernetes Objects

**Workload Objects:**
- **Pod:** Smallest deployable unit (one or more containers)
- **Deployment:** Manages pod replicas and updates
- **ReplicaSet:** Ensures specified number of pod replicas
- **StatefulSet:** Manages stateful applications
- **DaemonSet:** Ensures pod runs on all/some nodes
- **Job:** Runs a task to completion
- **CronJob:** Runs jobs on a schedule

**Service Objects:**
- **Service:** Exposes pods as network service
- **Ingress:** Manages external HTTP/HTTPS access

**Configuration Objects:**
- **ConfigMap:** Stores configuration data
- **Secret:** Stores sensitive data
- **Namespace:** Logical grouping of resources

**Storage Objects:**
- **PersistentVolume:** Cluster-wide storage
- **PersistentVolumeClaim:** Request for storage
- **StorageClass:** Defines storage classes

**Policy Objects:**
- **ResourceQuota:** Limits resource usage
- **LimitRange:** Constraints on resources
- **NetworkPolicy:** Network access control

#### Object Management Methods

Kubernetes provides three ways to manage objects:

**1. Imperative Commands**
- Direct commands that immediately perform operations
- Good for learning and quick tasks
- Not recommended for production

```bash
# Create object
kubectl create deployment myapp --image=nginx

# Update object
kubectl scale deployment myapp --replicas=3

# Delete object
kubectl delete deployment myapp
```

**2. Imperative Object Configuration**
- Commands that specify the operation and configuration
- Configuration stored in files
- Operations are explicit

```bash
# Create from file
kubectl create -f deployment.yaml

# Replace object
kubectl replace -f deployment.yaml

# Delete object
kubectl delete -f deployment.yaml
```

**3. Declarative Object Configuration (Recommended)**
- You describe desired state in files
- Kubernetes determines operations needed
- Best for production and version control

```bash
# Apply configuration
kubectl apply -f deployment.yaml

# Kubernetes automatically:
# - Creates object if it doesn't exist
# - Updates object if it exists
# - Maintains desired state
```

#### Object YAML Structure

All Kubernetes objects follow a similar YAML structure:

```yaml
apiVersion: v1          # API version
kind: Pod              # Object type
metadata:              # Object metadata
  name: my-pod         # Object name
  namespace: default   # Namespace (optional)
  labels:              # Labels (optional)
    app: nginx
spec:                  # Desired state
  # Object-specific configuration
status:                # Actual state (read-only, managed by K8s)
  # Current state
```

**Required Fields:**
- `apiVersion`: Which version of Kubernetes API
- `kind`: What kind of object
- `metadata`: Object identification (name, namespace, labels)
- `spec`: Desired state (varies by object type)

#### Object Metadata

**Common Metadata Fields:**

```yaml
metadata:
  name: my-pod                    # Object name (required)
  namespace: default              # Namespace (optional)
  labels:                         # Key-value pairs for selection
    app: nginx
    env: production
  annotations:                    # Non-identifying metadata
    description: "Web server pod"
  uid: "123e4567-e89b-12d3..."   # Unique ID (auto-generated)
  resourceVersion: "12345"        # Version for optimistic concurrency
  generation: 1                   # Generation number
  creationTimestamp: "2024-01-01T00:00:00Z"
```

**Labels:**
- Key-value pairs attached to objects
- Used for selection and organization
- Example: `app=nginx`, `env=production`

**Annotations:**
- Key-value pairs for metadata
- Not used for selection
- Example: `description`, `contact-info`

#### Object Lifecycle

**Lifecycle Stages:**

1. **Creation:** Object created via API
2. **Validation:** Kubernetes validates the object
3. **Storage:** Object stored in etcd
4. **Reconciliation:** Controllers reconcile desired vs actual state
5. **Deletion:** Object marked for deletion
6. **Finalization:** Finalizers run (if any)
7. **Removal:** Object removed from etcd

#### Managing Objects with kubectl

**Viewing Objects:**

```bash
# List objects
kubectl get <object-type>
kubectl get pods
kubectl get deployments

# Get specific object
kubectl get <object-type> <object-name>
kubectl get pod my-pod

# Describe object (detailed info)
kubectl describe <object-type> <object-name>
kubectl describe pod my-pod

# Get object YAML
kubectl get <object-type> <object-name> -o yaml

# Get object JSON
kubectl get <object-type> <object-name> -o json
```

**Creating Objects:**

```bash
# From YAML file
kubectl apply -f object.yaml
kubectl create -f object.yaml

# From stdin
cat object.yaml | kubectl apply -f -

# Multiple files
kubectl apply -f file1.yaml -f file2.yaml

# Directory
kubectl apply -f ./manifests/
```

**Updating Objects:**

```bash
# Apply changes (declarative)
kubectl apply -f updated-object.yaml

# Edit object interactively
kubectl edit <object-type> <object-name>

# Patch object
kubectl patch <object-type> <object-name> -p '{"spec":{"replicas":3}}'

# Replace object
kubectl replace -f object.yaml
```

**Deleting Objects:**

```bash
# Delete by name
kubectl delete <object-type> <object-name>

# Delete from file
kubectl delete -f object.yaml

# Delete by label
kubectl delete <object-type> -l app=nginx

# Delete all objects of type
kubectl delete <object-type> --all
```

#### Object Relationships

Objects in Kubernetes often relate to each other:

**Owner References:**
- Objects can have owners
- Deleting owner deletes owned objects (cascading deletion)
- Example: ReplicaSet owns Pods

**Labels and Selectors:**
- Objects use labels to identify related objects
- Selectors match labels
- Example: Service selects Pods by labels

**Dependencies:**
- Some objects depend on others
- Example: Pod depends on ConfigMap/Secret

#### Best Practices for Object Management

**1. Use Declarative Configuration**
```bash
# ✅ Good: Declarative
kubectl apply -f deployment.yaml

# ⚠️ Avoid: Imperative
kubectl create deployment myapp --image=nginx
```

**2. Version Control Your Configurations**
- Store YAML files in Git
- Track changes over time
- Enable rollback capability

**3. Use Meaningful Names**
```yaml
# ✅ Good
name: frontend-deployment

# ⚠️ Avoid
name: app1
```

**4. Use Labels Consistently**
```yaml
labels:
  app: frontend
  env: production
  team: web
```

**5. Organize with Namespaces**
- Group related objects in namespaces
- Separate environments
- Apply policies per namespace

**6. Use kubectl apply**
- Idempotent operations
- Handles creation and updates
- Better for automation

#### Common kubectl Commands

```bash
# Get objects
kubectl get <resource>
kubectl get pods
kubectl get deployments
kubectl get services

# Describe object
kubectl describe <resource> <name>

# Create/Apply
kubectl apply -f <file>
kubectl create -f <file>

# Edit
kubectl edit <resource> <name>

# Delete
kubectl delete <resource> <name>
kubectl delete -f <file>

# Watch
kubectl get <resource> -w

# Output formats
kubectl get <resource> -o yaml
kubectl get <resource> -o json
kubectl get <resource> -o wide
```

#### Key Takeaways

1. **Objects are persistent entities** - Represent cluster state
2. **Declarative approach** - Describe desired state, Kubernetes makes it happen
3. **spec vs status** - spec is desired, status is actual
4. **Use kubectl apply** - Recommended for object management
5. **Version control** - Store YAML files in Git
6. **Labels and selectors** - Organize and select objects
7. **Namespaces** - Organize objects logically
8. **Object relationships** - Objects relate through owners and selectors

Understanding Kubernetes objects and their management is fundamental to working with Kubernetes effectively.

---

### Field Selectors

**Field Selectors** allow you to filter Kubernetes objects based on field values. They provide a way to select objects using specific field values rather than labels.

#### What are Field Selectors?

Field selectors let you select Kubernetes objects based on the values of one or more resource fields. Unlike label selectors, field selectors filter by actual field values in the object.

**Key Characteristics:**
- Filter objects by field values
- Different from label selectors
- Support different fields per resource type
- Used with `kubectl get` commands

#### Field Selector Syntax

```bash
# Basic syntax
kubectl get <resource> --field-selector <field>=<value>

# Multiple selectors (AND logic)
kubectl get <resource> --field-selector <field1>=<value1>,<field2>=<value2>
```

#### Common Field Selectors

**For Pods:**

```bash
# Select pods by node
kubectl get pods --field-selector spec.nodeName=node-1

# Select pods by phase (Running, Pending, Succeeded, Failed)
kubectl get pods --field-selector status.phase=Running

# Select pods not assigned to any node
kubectl get pods --field-selector spec.nodeName=

# Multiple selectors
kubectl get pods --field-selector status.phase=Running,spec.nodeName=node-1
```

**For Nodes:**

```bash
# Select nodes by condition
kubectl get nodes --field-selector status.conditions[?(@.type=="Ready")].status=True

# Select nodes by unschedulable status
kubectl get nodes --field-selector spec.unschedulable=false
```

**For PersistentVolumes:**

```bash
# Select by phase
kubectl get pv --field-selector status.phase=Available

# Select by storage class
kubectl get pv --field-selector spec.storageClassName=fast-ssd
```

#### Field Selector vs Label Selector

| Aspect | Field Selector | Label Selector |
|--------|---------------|----------------|
| **Syntax** | `--field-selector` | `-l` or `--selector` |
| **Filters by** | Object field values | Label key-value pairs |
| **Use Case** | System fields (status, spec) | Custom organization |
| **Examples** | `status.phase=Running` | `app=nginx` |
| **Flexibility** | Limited to specific fields | Any custom labels |

**When to Use Field Selectors:**
- Filter by system fields (status, phase, node assignment)
- Find objects in specific states
- Filter by infrastructure-related fields

**When to Use Label Selectors:**
- Filter by application/team/environment
- Custom organization and grouping
- Application-specific filtering

#### Practical Examples

**Example 1: Find Running Pods**

```bash
# Get all running pods
kubectl get pods --field-selector status.phase=Running

# Get running pods in specific namespace
kubectl get pods -n production --field-selector status.phase=Running
```

**Example 2: Find Pods on Specific Node**

```bash
# Get all pods on node-1
kubectl get pods --field-selector spec.nodeName=node-1

# Get pods on node-1 that are running
kubectl get pods --field-selector spec.nodeName=node-1,status.phase=Running
```

**Example 3: Find Unassigned Pods**

```bash
# Pods not yet assigned to a node
kubectl get pods --field-selector spec.nodeName=

# Useful for debugging scheduling issues
```

**Example 4: Find Available PersistentVolumes**

```bash
# Get available persistent volumes
kubectl get pv --field-selector status.phase=Available

# Get bound persistent volumes
kubectl get pv --field-selector status.phase=Bound
```

**Example 5: Combine with Other Filters**

```bash
# Running pods in production namespace on node-1
kubectl get pods -n production \
  --field-selector status.phase=Running,spec.nodeName=node-1

# With label selector
kubectl get pods -l app=nginx \
  --field-selector status.phase=Running
```

#### Available Field Selectors by Resource

**Pods:**
- `spec.nodeName` - Node name
- `spec.restartPolicy` - Restart policy
- `status.phase` - Pod phase (Pending, Running, Succeeded, Failed)
- `status.podIP` - Pod IP address
- `metadata.namespace` - Namespace

**Nodes:**
- `metadata.name` - Node name
- `spec.unschedulable` - Schedulable status

**PersistentVolumes:**
- `status.phase` - Volume phase (Available, Bound, Released, Failed)
- `spec.storageClassName` - Storage class name

**PersistentVolumeClaims:**
- `status.phase` - Claim phase
- `spec.storageClassName` - Storage class name

#### Field Selector Limitations

**Not All Fields are Supported:**
- Only specific fields can be used in field selectors
- Complex fields (arrays, nested objects) may not be supported
- Field selector support varies by resource type

**Comparison Operators:**
- Field selectors support equality (`=`) only
- No support for `!=`, `>`, `<`, `in`, `notin` like label selectors
- For complex filtering, use label selectors or `jq`/`grep`

#### Combining Field and Label Selectors

You can combine both selectors for powerful filtering:

```bash
# Pods with label app=nginx that are running
kubectl get pods -l app=nginx --field-selector status.phase=Running

# Pods with label env=production on node-1
kubectl get pods -l env=production --field-selector spec.nodeName=node-1
```

#### Field Selector Best Practices

1. **Use for System Fields**
   - Status, phase, node assignment
   - Infrastructure-related filtering

2. **Combine with Labels**
   - Use field selectors for system state
   - Use label selectors for application logic

3. **Know the Limitations**
   - Not all fields are supported
   - Only equality comparison

4. **Use for Debugging**
   - Find pods in specific states
   - Debug scheduling issues
   - Monitor resource status

#### Field Selector Commands Summary

```bash
# Basic field selector
kubectl get <resource> --field-selector <field>=<value>

# Multiple field selectors
kubectl get <resource> --field-selector <field1>=<value1>,<field2>=<value2>

# Combine with label selector
kubectl get <resource> -l <label> --field-selector <field>=<value>

# Common pod field selectors
kubectl get pods --field-selector status.phase=Running
kubectl get pods --field-selector spec.nodeName=node-1
kubectl get pods --field-selector spec.nodeName=

# Common PV field selectors
kubectl get pv --field-selector status.phase=Available
```

#### Key Takeaways

1. **Field selectors filter by field values** - Not labels
2. **Use for system fields** - Status, phase, node assignment
3. **Limited to equality** - Only `=` operator supported
4. **Combine with labels** - Use both for powerful filtering
5. **Not all fields supported** - Check documentation for available fields
6. **Useful for debugging** - Find objects in specific states
7. **Different from label selectors** - Each has its use case

Field selectors are a powerful tool for filtering Kubernetes objects based on their actual field values, especially useful for system-level filtering and debugging.

---

### Namespaces

**Namespaces** in Kubernetes provide a way to divide cluster resources between multiple users, teams, or projects. Think of namespaces as virtual clusters within a physical Kubernetes cluster.

#### What are Namespaces?

Namespaces are a mechanism for organizing and isolating resources in a Kubernetes cluster. They provide:

- **Resource Isolation:** Resources in different namespaces are isolated from each other
- **Resource Quotas:** Set resource limits per namespace
- **Access Control:** Apply RBAC policies per namespace
- **Organization:** Group related resources together

#### Default Namespaces

Kubernetes comes with several built-in namespaces:

1. **default**
   - Default namespace for resources when no namespace is specified
   - Where your resources go if you don't specify a namespace

2. **kube-system**
   - System namespace for Kubernetes system components
   - Contains system pods (kube-proxy, kube-dns, etc.)
   - ⚠️ **Don't create user resources here**

3. **kube-public**
   - Publicly accessible namespace
   - Contains cluster information readable by all users
   - Rarely used

4. **kube-node-lease**
   - Used for node heartbeat
   - Helps detect node failures
   - Managed by Kubernetes automatically

#### Why Use Namespaces?

**1. Resource Organization**
```
Production Environment
├── production namespace
│   ├── frontend pods
│   ├── backend pods
│   └── database pods
└── staging namespace
    ├── frontend pods
    ├── backend pods
    └── database pods
```

**2. Resource Quotas**
- Limit CPU and memory per namespace
- Prevent one team from consuming all resources
- Enforce resource limits per project

**3. Access Control**
- Different teams can have access to different namespaces
- Isolate sensitive environments (production)
- Apply different security policies

**4. Environment Separation**
- Separate development, staging, and production
- Avoid conflicts between environments
- Easy to clean up (delete namespace)

#### Creating Namespaces

**Method 1: Using kubectl**

```bash
# Create namespace
kubectl create namespace <namespace-name>

# Example
kubectl create namespace development
kubectl create namespace production
```

**Method 2: Using YAML**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: development
```

```bash
# Apply YAML
kubectl apply -f namespace.yaml
```

**Example Reference:**
- **[nginx/namespace.yml](./nginx/namespace.yml)** - Example namespace YAML file for nginx namespace

**Method 3: Using kubectl with YAML (one-liner)**

```bash
kubectl create namespace development --dry-run=client -o yaml | kubectl apply -f -
```

#### Viewing Namespaces

```bash
# List all namespaces
kubectl get namespaces
# or
kubectl get ns

# View specific namespace
kubectl get namespace <namespace-name>
# or
kubectl get ns <namespace-name>

# Describe namespace
kubectl describe namespace <namespace-name>
```

#### Working with Namespaces

**Creating Resources in a Namespace**

```bash
# Method 1: Use --namespace or -n flag
kubectl create deployment myapp --image=nginx -n development

# Method 2: Set default namespace context
kubectl config set-context --current --namespace=development
kubectl create deployment myapp --image=nginx

# Method 3: Specify in YAML
```

**YAML Example:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: development  # Specify namespace here
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

**Viewing Resources in Namespaces**

```bash
# List pods in specific namespace
kubectl get pods -n development

# List all resources in namespace
kubectl get all -n development

# List resources across all namespaces
kubectl get pods --all-namespaces
# or
kubectl get pods -A
```

**Switching Namespace Context**

```bash
# Set default namespace for current context
kubectl config set-context --current --namespace=development

# Verify current namespace
kubectl config view --minify | grep namespace

# List current namespace
kubectl config view --minify -o jsonpath='{..namespace}'
```

#### Deleting Namespaces

```bash
# Delete namespace (deletes all resources in it)
kubectl delete namespace <namespace-name>

# Example
kubectl delete namespace development

# ⚠️ Warning: This deletes ALL resources in the namespace!
```

**Important Notes:**
- ⚠️ Deleting a namespace deletes **all resources** in that namespace
- ⚠️ Default and kube-system namespaces **cannot be deleted**
- ⚠️ Namespace deletion is asynchronous (may take time)

#### Namespace Best Practices

**1. Use Namespaces for Environments**
```
development namespace → Development environment
staging namespace → Staging environment
production namespace → Production environment
```

**2. Use Namespaces for Teams**
```
team-a namespace → Team A's resources
team-b namespace → Team B's resources
```

**3. Use Namespaces for Applications**
```
app-frontend namespace → Frontend application
app-backend namespace → Backend application
```

**4. Set Resource Quotas**
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: development
spec:
  hard:
    requests.cpu: "4"
    requests.memory: 8Gi
    limits.cpu: "8"
    limits.memory: 16Gi
```

**5. Apply RBAC Policies**
- Use Role and RoleBinding for namespace-level permissions
- Grant access only to necessary namespaces

#### Common Namespace Patterns

**Pattern 1: Environment-Based**
```
production
staging
development
testing
```

**Pattern 2: Team-Based**
```
team-frontend
team-backend
team-devops
```

**Pattern 3: Application-Based**
```
app-user-service
app-order-service
app-payment-service
```

**Pattern 4: Hybrid**
```
production-frontend
production-backend
staging-frontend
staging-backend
```

#### Namespace Limitations

**What Namespaces DON'T Provide:**
- ❌ **Network Isolation:** Pods in different namespaces can still communicate
- ❌ **Complete Security:** Not a security boundary by itself
- ❌ **Resource Isolation:** Resources are still in the same cluster

**What You Need for True Isolation:**
- Network Policies (for network isolation)
- RBAC (for access control)
- Resource Quotas (for resource limits)

#### Namespace Commands Summary

```bash
# Create
kubectl create namespace <name>

# List
kubectl get namespaces
kubectl get ns

# Describe
kubectl describe namespace <name>

# Delete
kubectl delete namespace <name>

# Set default
kubectl config set-context --current --namespace=<name>

# View resources in namespace
kubectl get <resource> -n <namespace>

# View resources in all namespaces
kubectl get <resource> --all-namespaces
kubectl get <resource> -A
```

#### Practical Examples

**Example 1: Create Development Environment**

```bash
# Create namespace
kubectl create namespace development

# Create deployment in development namespace
kubectl create deployment myapp --image=nginx -n development

# View pods in development
kubectl get pods -n development
```

**Example 2: Switch Between Namespaces**

```bash
# Work in development
kubectl config set-context --current --namespace=development
kubectl get pods

# Switch to production
kubectl config set-context --current --namespace=production
kubectl get pods
```

**Example 3: Compare Resources Across Namespaces**

```bash
# View all pods across namespaces
kubectl get pods --all-namespaces

# View deployments in specific namespaces
kubectl get deployments -n development
kubectl get deployments -n production
```

#### Example Reference

For a practical example of a namespace YAML file, check out:

- **[nginx/namespace.yml](./nginx/namespace.yml)** - Example namespace definition for creating an nginx namespace

This example demonstrates:
- Basic namespace YAML structure
- How to define a namespace with metadata
- Simple namespace creation pattern

#### Key Takeaways

1. **Namespaces organize resources** - Group related resources together
2. **Namespaces provide isolation** - Separate environments, teams, or applications
3. **Use --namespace or -n flag** - Specify namespace in commands
4. **Set default namespace** - Use `kubectl config set-context` for convenience
5. **Namespaces don't provide network isolation** - Use Network Policies for that
6. **Delete namespace carefully** - It deletes all resources in it
7. **Use Resource Quotas** - Limit resources per namespace
8. **Apply RBAC** - Control access per namespace

Namespaces are a fundamental concept in Kubernetes that help you organize and manage resources effectively in your cluster.

---

### Pods

**Pods** are the smallest deployable units in Kubernetes. A Pod represents a single instance of a running process in your cluster and can contain one or more containers.

#### What is a Pod?

A Pod is a group of one or more containers that:
- Share the same network namespace (same IP address)
- Share the same storage volumes
- Are scheduled together on the same node
- Have the same lifecycle (created, started, stopped together)

**Key Characteristics:**
- **Smallest unit:** Cannot split a Pod across nodes
- **Ephemeral:** Pods are created and destroyed, not updated
- **One IP per Pod:** All containers in a Pod share the same IP
- **Localhost communication:** Containers in same Pod can communicate via localhost

#### Pod Structure

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: default
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

#### Single Container Pods

Most common use case - one container per Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

#### Multi-Container Pods

Pods can contain multiple containers that work together:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
  - name: sidecar
    image: busybox:latest
    command: ['sh', '-c', 'while true; do echo hello; sleep 10; done']
```

**Use Cases for Multi-Container Pods:**
- **Sidecar pattern:** Helper container (logging, monitoring)
- **Adapter pattern:** Transform data format
- **Ambassador pattern:** Proxy network requests

#### Pod Lifecycle

**Pod Phases:**

1. **Pending:** Pod accepted by cluster, but containers not created
2. **Running:** Pod bound to node, all containers created, at least one running
3. **Succeeded:** All containers terminated successfully
4. **Failed:** At least one container terminated with failure
5. **Unknown:** Pod state cannot be determined

**Pod States:**

```bash
# View pod phases
kubectl get pods

# Output shows:
# NAME        READY   STATUS    RESTARTS   AGE
# my-pod      1/1     Running   0          5m
```

#### Creating Pods

**Method 1: Using kubectl**

```bash
# Create pod from YAML
kubectl create -f pod.yaml

# Create pod imperatively
kubectl run nginx-pod --image=nginx:latest

# Create pod with namespace
kubectl create -f pod.yaml -n my-namespace
```

**Method 2: Using YAML (Recommended)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

```bash
kubectl apply -f pod.yaml
```

**Method 3: Using kubectl run**

```bash
# Basic pod
kubectl run my-pod --image=nginx:latest

# With labels
kubectl run my-pod --image=nginx:latest --labels="app=nginx,env=prod"

# With namespace
kubectl run my-pod --image=nginx:latest -n my-namespace
```

#### Viewing Pods

```bash
# List all pods
kubectl get pods

# List pods in namespace
kubectl get pods -n my-namespace

# List pods across all namespaces
kubectl get pods --all-namespaces
kubectl get pods -A

# Get pod details
kubectl get pod <pod-name>

# Describe pod (detailed information)
kubectl describe pod <pod-name>

# Get pod YAML
kubectl get pod <pod-name> -o yaml

# Get pod JSON
kubectl get pod <pod-name> -o json

# Watch pods
kubectl get pods -w
```

#### Pod Logs

```bash
# View pod logs
kubectl logs <pod-name>

# View logs from specific container (multi-container pod)
kubectl logs <pod-name> -c <container-name>

# Follow logs (like tail -f)
kubectl logs -f <pod-name>

# View logs from previous container instance (if crashed)
kubectl logs <pod-name> --previous

# View last N lines
kubectl logs <pod-name> --tail=100

# View logs since timestamp
kubectl logs <pod-name> --since=10m
```

#### Executing Commands in Pods

```bash
# Execute command in pod
kubectl exec <pod-name> -- <command>

# Example
kubectl exec nginx-pod -- ls /usr/share/nginx/html

# Interactive shell
kubectl exec -it <pod-name> -- /bin/bash

# Execute in specific container (multi-container pod)
kubectl exec -it <pod-name> -c <container-name> -- /bin/bash
```

#### Pod Configuration

**Container Image:**

```yaml
spec:
  containers:
  - name: nginx
    image: nginx:latest          # Use specific tag
    imagePullPolicy: Always     # Always, IfNotPresent, Never
```

**Container Ports:**

```yaml
spec:
  containers:
  - name: nginx
    ports:
    - containerPort: 80
      protocol: TCP
      name: http
    - containerPort: 443
      protocol: TCP
      name: https
```

**Environment Variables:**

```yaml
spec:
  containers:
  - name: nginx
    env:
    - name: ENV_VAR
      value: "value"
    - name: DB_HOST
      valueFrom:
        configMapKeyRef:
          name: my-config
          key: db-host
    - name: SECRET_KEY
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: password
```

**Resource Limits:**

```yaml
spec:
  containers:
  - name: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

**Volume Mounts:**

```yaml
spec:
  containers:
  - name: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/nginx
  volumes:
  - name: config-volume
    configMap:
      name: nginx-config
```

#### Pod Restart Policy

```yaml
spec:
  restartPolicy: Always    # Always, OnFailure, Never
  containers:
  - name: nginx
    image: nginx:latest
```

**Restart Policies:**
- **Always:** Always restart container (default for Pods)
- **OnFailure:** Restart only on failure
- **Never:** Never restart container

#### Pod Health Checks

**Liveness Probe:**

```yaml
spec:
  containers:
  - name: nginx
    livenessProbe:
      httpGet:
        path: /health
        port: 80
      initialDelaySeconds: 30
      periodSeconds: 10
      timeoutSeconds: 5
      failureThreshold: 3
```

**Readiness Probe:**

```yaml
spec:
  containers:
  - name: nginx
    readinessProbe:
      httpGet:
        path: /ready
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
```

**Startup Probe:**

```yaml
spec:
  containers:
  - name: nginx
    startupProbe:
      httpGet:
        path: /startup
        port: 80
      failureThreshold: 30
      periodSeconds: 10
```

**Probe Types:**
- **httpGet:** HTTP GET request
- **tcpSocket:** TCP connection check
- **exec:** Execute command

#### Pod Status and Conditions

**View Pod Status:**

```bash
# Get pod status
kubectl get pod <pod-name> -o jsonpath='{.status.phase}'

# Get pod conditions
kubectl get pod <pod-name> -o jsonpath='{.status.conditions}'

# Get pod IP
kubectl get pod <pod-name> -o jsonpath='{.status.podIP}'

# Get node name
kubectl get pod <pod-name> -o jsonpath='{.spec.nodeName}'
```

**Pod Conditions:**
- **PodScheduled:** Pod assigned to node
- **Initialized:** Init containers completed
- **ContainersReady:** All containers ready
- **Ready:** Pod ready to serve traffic

#### Pod Networking

**Pod IP:**
- Each Pod gets its own IP address
- Containers in same Pod share the same IP
- Pods can communicate using Pod IPs

**Port Forwarding:**

```bash
# Forward local port to pod
kubectl port-forward <pod-name> 8080:80

# Access pod on localhost:8080
curl http://localhost:8080
```

**Pod-to-Pod Communication:**
- Pods can communicate using Pod IPs
- Use Service for stable networking
- Network policies control access

#### Pod Storage

**Ephemeral Storage:**
- Pods have ephemeral storage
- Data lost when Pod is deleted
- Use volumes for persistence

**Volume Types:**

```yaml
spec:
  containers:
  - name: nginx
    volumeMounts:
    - name: data
      mountPath: /data
  volumes:
  - name: data
    emptyDir: {}              # Temporary storage
  # - name: data
  #   persistentVolumeClaim:
  #     claimName: my-pvc     # Persistent storage
```

#### Pod Security

**Security Context:**

```yaml
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
  - name: nginx
    securityContext:
      allowPrivilegeEscalation: false
      capabilities:
        drop:
        - ALL
        add:
        - NET_BIND_SERVICE
      readOnlyRootFilesystem: true
```

**Service Account:**

```yaml
spec:
  serviceAccountName: my-service-account
  containers:
  - name: nginx
    image: nginx:latest
```

#### Pod Scheduling

**Node Selection:**

```yaml
spec:
  nodeSelector:
    disktype: ssd
    zone: us-east-1a
```

**Node Affinity:**

```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: zone
            operator: In
            values:
            - us-east-1a
```

**Pod Affinity:**

```yaml
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - nginx
        topologyKey: kubernetes.io/hostname
```

**Taints and Tolerations:**

```yaml
spec:
  tolerations:
  - key: "key1"
    operator: "Equal"
    value: "value1"
    effect: "NoSchedule"
```

#### Init Containers

Init containers run before the main containers:

```yaml
spec:
  initContainers:
  - name: init-db
    image: busybox
    command: ['sh', '-c', 'until nslookup db-service; do echo waiting; sleep 2; done']
  containers:
  - name: nginx
    image: nginx:latest
```

**Use Cases:**
- Wait for dependencies
- Initialize data
- Run setup scripts

#### Pod Deletion

```bash
# Delete pod
kubectl delete pod <pod-name>

# Delete pod by file
kubectl delete -f pod.yaml

# Force delete (immediate)
kubectl delete pod <pod-name> --force --grace-period=0

# Delete all pods in namespace
kubectl delete pods --all -n my-namespace
```

**Graceful Termination:**
- Pod receives SIGTERM signal
- Grace period (default 30 seconds)
- Then SIGKILL if not terminated

#### Pod Best Practices

**1. Don't Create Pods Directly**
- Use Deployments, StatefulSets, or DaemonSets
- Pods are ephemeral and not self-healing

**2. Use Specific Image Tags**
```yaml
# ✅ Good
image: nginx:1.21.6

# ⚠️ Avoid
image: nginx:latest
```

**3. Set Resource Limits**
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

**4. Use Health Checks**
```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 80
readinessProbe:
  httpGet:
    path: /ready
    port: 80
```

**5. Use Labels**
```yaml
metadata:
  labels:
    app: nginx
    env: production
    version: v1.0
```

**6. Use Namespaces**
```yaml
metadata:
  namespace: production
```

**7. Set Security Context**
```yaml
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
```

#### Common Pod Issues and Troubleshooting

**Pod Pending:**
```bash
# Check why pod is pending
kubectl describe pod <pod-name>

# Common causes:
# - Insufficient resources
# - Node selector/affinity not matching
# - Taints without tolerations
```

**Pod CrashLoopBackOff:**
```bash
# Check pod logs
kubectl logs <pod-name>

# Check previous container logs
kubectl logs <pod-name> --previous

# Describe pod for events
kubectl describe pod <pod-name>
```

**Pod Not Ready:**
```bash
# Check readiness probe
kubectl describe pod <pod-name>

# Check container status
kubectl get pod <pod-name> -o jsonpath='{.status.containerStatuses}'
```

**Image Pull Errors:**
```bash
# Check image pull policy
kubectl get pod <pod-name> -o jsonpath='{.spec.containers[0].imagePullPolicy}'

# Common issues:
# - Image doesn't exist
# - Image pull secrets missing
# - Network issues
```

#### Advanced Pod Features

**Pod Disruption Budget:**

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: nginx
```

**Pod Priority:**

```yaml
apiVersion: v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000
---
spec:
  priorityClassName: high-priority
```

**Pod Preset (Deprecated):**
- Pod Presets are deprecated
- Use Init Containers or Admission Controllers instead

#### Pod Commands Summary

```bash
# Create
kubectl create -f pod.yaml
kubectl run <name> --image=<image>

# Get
kubectl get pods
kubectl get pod <name>
kubectl describe pod <name>

# Logs
kubectl logs <pod-name>
kubectl logs -f <pod-name>

# Exec
kubectl exec -it <pod-name> -- /bin/bash

# Port forward
kubectl port-forward <pod-name> 8080:80

# Delete
kubectl delete pod <name>
kubectl delete -f pod.yaml

# Edit
kubectl edit pod <name>
```

#### Example Reference

For a practical example of a Pod YAML file, check out:

- **[nginx/pod.yml](./nginx/pod.yml)** - Example Pod definition for nginx

This example demonstrates:
- Basic Pod structure with metadata
- Container specification with image
- Port configuration
- Namespace assignment
- Simple single-container Pod pattern

**To use this example:**
```bash
# Apply the pod
kubectl apply -f nginx/pod.yml

# View the pod
kubectl get pods -n nginx

# Check pod status
kubectl describe pod nginx-pod -n nginx

# View logs
kubectl logs nginx-pod -n nginx

# Access pod
kubectl exec -it nginx-pod -n nginx -- /bin/bash
```

#### Key Takeaways

1. **Pods are the smallest deployable unit** - Cannot split across nodes
2. **Pods are ephemeral** - Created and destroyed, not updated
3. **Containers in Pod share network and storage** - Same IP, shared volumes
4. **Don't create Pods directly** - Use Deployments, StatefulSets, or DaemonSets
5. **Set resource limits** - Prevent resource exhaustion
6. **Use health checks** - Liveness and readiness probes
7. **Pods get their own IP** - All containers in Pod share the IP
8. **Use labels** - For organization and selection
9. **Pods are not self-healing** - Use controllers for reliability
10. **One container per Pod is common** - Multi-container Pods for specific use cases

Pods are the fundamental building blocks of Kubernetes applications. Understanding Pods is essential for working with Kubernetes effectively.

---

### Labels and Selectors

**Labels** and **Selectors** are fundamental concepts in Kubernetes that enable you to organize and select groups of objects. They are key-value pairs attached to objects for identification and selection.

#### What are Labels?

**Labels** are key-value pairs attached to Kubernetes objects (Pods, Services, Deployments, etc.) that are used to:
- **Organize objects** - Group related objects
- **Identify objects** - Mark objects with meaningful attributes
- **Select objects** - Find and operate on groups of objects
- **Filter objects** - Query and filter based on labels

**Key Characteristics:**
- **Key-Value Pairs:** Simple string key-value pairs
- **Attached to Objects:** Part of object metadata
- **Not Unique:** Multiple objects can have same labels
- **Queryable:** Can be used to select objects

#### Label Syntax

**Label Format:**
- **Key:** Alphanumeric, hyphens, underscores, dots
- **Value:** Alphanumeric, hyphens, underscores, dots
- **Length:** Max 63 characters each
- **Prefix:** Optional prefix (e.g., `kubernetes.io/`, `app.kubernetes.io/`)

**Valid Examples:**
```yaml
labels:
  app: nginx
  env: production
  version: "1.0"
  tier: frontend
  app.kubernetes.io/name: nginx
  app.kubernetes.io/version: "1.21"
```

**Invalid Examples:**
```yaml
labels:
  app/name: nginx        # ❌ Slash not allowed in key (unless prefix)
  app name: nginx        # ❌ Space not allowed
  app: nginx:latest      # ❌ Colon not allowed in value
```

#### Adding Labels to Objects

**Method 1: In YAML**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
    env: production
    version: "1.0"
    tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

**Method 2: Using kubectl**

```bash
# Add label to existing object
kubectl label pod nginx-pod env=production

# Add multiple labels
kubectl label pod nginx-pod env=production tier=frontend

# Overwrite existing label
kubectl label pod nginx-pod env=staging --overwrite

# Remove label
kubectl label pod nginx-pod env-
```

#### Viewing Labels

```bash
# List objects with labels
kubectl get pods --show-labels

# Show specific labels
kubectl get pods -L app,env

# Get label value
kubectl get pod nginx-pod -o jsonpath='{.metadata.labels.app}'
```

#### Common Label Conventions

**Recommended Labels (by Kubernetes):**

```yaml
labels:
  app.kubernetes.io/name: nginx
  app.kubernetes.io/instance: nginx-prod
  app.kubernetes.io/version: "1.21"
  app.kubernetes.io/component: web
  app.kubernetes.io/part-of: myapp
  app.kubernetes.io/managed-by: kubectl
```

**Common Custom Labels:**

```yaml
labels:
  app: nginx
  env: production
  version: "1.0"
  tier: frontend
  team: web
  region: us-east-1
```

#### What are Selectors?

**Selectors** are expressions used to select objects based on their labels. They allow you to:
- **Find objects** - Query objects by labels
- **Group objects** - Operate on groups of objects
- **Link objects** - Connect related objects (e.g., Service to Pods)

**Types of Selectors:**
1. **Equality-based** - Match exact label values
2. **Set-based** - Match using operators (In, NotIn, Exists, DoesNotExist)

#### Label Selectors

**Equality-Based Selectors:**

```yaml
selector:
  matchLabels:
    app: nginx
    env: production
```

**Set-Based Selectors:**

```yaml
selector:
  matchExpressions:
  - key: app
    operator: In
    values:
    - nginx
    - apache
  - key: env
    operator: NotIn
    values:
    - test
    - dev
  - key: tier
    operator: Exists
  - key: version
    operator: DoesNotExist
```

**Operators:**
- **In:** Value in list
- **NotIn:** Value not in list
- **Exists:** Key exists (no values needed)
- **DoesNotExist:** Key doesn't exist (no values needed)

#### Using Selectors with kubectl

**Equality-Based:**

```bash
# Select pods with label app=nginx
kubectl get pods -l app=nginx

# Select pods with multiple labels
kubectl get pods -l app=nginx,env=production

# Select pods without label
kubectl get pods -l '!env'

# Select pods with env not equal to test
kubectl get pods -l 'env!=test'
```

**Set-Based:**

```bash
# Select pods where app is nginx or apache
kubectl get pods -l 'app in (nginx,apache)'

# Select pods where env is not test or dev
kubectl get pods -l 'env notin (test,dev)'

# Select pods where tier exists
kubectl get pods -l 'tier'

# Select pods where version doesn't exist
kubectl get pods -l '!version'
```

#### Selectors in Kubernetes Objects

**ReplicaSet Selector:**

```yaml
apiVersion: apps/v1
kind: ReplicaSet
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx  # Must match selector
```

**Service Selector:**

```yaml
apiVersion: v1
kind: Service
spec:
  selector:
    app: nginx
    tier: frontend
```

**Deployment Selector:**

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  selector:
    matchLabels:
      app: nginx
    matchExpressions:
    - key: env
      operator: In
      values:
      - production
      - staging
```

#### Label Selector Examples

**Example 1: Simple Selection**

```yaml
# Pod with labels
metadata:
  labels:
    app: nginx
    env: production

# Service selecting the pod
spec:
  selector:
    app: nginx
    env: production
```

**Example 2: Complex Selection**

```yaml
# Deployment with set-based selector
spec:
  selector:
    matchLabels:
      app: nginx
    matchExpressions:
    - key: env
      operator: In
      values:
      - production
      - staging
    - key: version
      operator: Exists
```

#### Label Best Practices

**1. Use Standard Labels**

```yaml
labels:
  app.kubernetes.io/name: nginx
  app.kubernetes.io/version: "1.21"
```

**2. Use Consistent Naming**

```yaml
# ✅ Good - Consistent
labels:
  app: nginx
  env: production

# ⚠️ Avoid - Inconsistent
labels:
  application: nginx
  environment: production
```

**3. Use Meaningful Labels**

```yaml
# ✅ Good
labels:
  app: frontend
  tier: web
  env: production

# ⚠️ Avoid
labels:
  a: f
  t: w
  e: p
```

**4. Don't Over-Label**

```yaml
# ✅ Good - Essential labels
labels:
  app: nginx
  env: production

# ⚠️ Avoid - Too many labels
labels:
  app: nginx
  env: production
  team: web
  region: us-east-1
  zone: us-east-1a
  instance: nginx-1
  # ... many more
```

**5. Use Labels for Organization**

```yaml
# Organize by application
labels:
  app: nginx

# Organize by environment
labels:
  env: production

# Organize by team
labels:
  team: frontend
```

#### Label Selector Matching

**Important Rules:**

1. **Selector must match labels** - Objects selected must have matching labels
2. **Template labels must match selector** - In ReplicaSet/Deployment, pod template labels must match selector
3. **Service selector matches pod labels** - Service selects pods by matching labels

**Example - ReplicaSet:**

```yaml
spec:
  selector:
    matchLabels:
      app: nginx        # Selector
  template:
    metadata:
      labels:
        app: nginx      # Must match selector
```

#### Label Selector Commands

```bash
# Get objects by label
kubectl get pods -l app=nginx
kubectl get pods -l app=nginx,env=production

# Get objects without label
kubectl get pods -l '!env'

# Get objects with label not equal
kubectl get pods -l 'env!=test'

# Get objects with set-based selector
kubectl get pods -l 'app in (nginx,apache)'
kubectl get pods -l 'env notin (test,dev)'
kubectl get pods -l 'tier'
kubectl get pods -l '!version'

# Add label
kubectl label pod nginx-pod env=production

# Remove label
kubectl label pod nginx-pod env-

# Update label
kubectl label pod nginx-pod env=staging --overwrite
```

#### Label Selector Use Cases

**1. Service to Pod Selection**

```yaml
# Service
spec:
  selector:
    app: nginx

# Pods (selected by service)
metadata:
  labels:
    app: nginx
```

**2. Deployment to Pod Selection**

```yaml
# Deployment
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
```

**3. Filtering and Querying**

```bash
# Get all production pods
kubectl get pods -l env=production

# Get all frontend pods
kubectl get pods -l tier=frontend

# Get pods in production or staging
kubectl get pods -l 'env in (production,staging)'
```

#### Key Takeaways

1. **Labels are key-value pairs** - Attached to objects for organization
2. **Selectors match labels** - Used to find and select objects
3. **Equality and set-based** - Two types of selectors
4. **Template labels must match selector** - In ReplicaSet/Deployment
5. **Use standard labels** - Follow Kubernetes conventions
6. **Be consistent** - Use consistent label names across objects
7. **Labels enable selection** - Services, Deployments use selectors
8. **Labels are queryable** - Use with kubectl -l flag

Labels and selectors are fundamental to how Kubernetes organizes and connects objects. Understanding them is essential for working with Kubernetes effectively.

---

### Annotations

**Annotations** are key-value pairs that can be attached to Kubernetes objects to store arbitrary, non-identifying metadata. Unlike labels, annotations are not used for selection or querying.

#### What are Annotations?

**Annotations** are metadata attached to Kubernetes objects that:
- **Store metadata** - Additional information about objects
- **Not for selection** - Unlike labels, not used for selection
- **Arbitrary data** - Can store any string data
- **Tool integration** - Used by tools and extensions

**Key Characteristics:**
- **Key-Value Pairs:** Similar to labels but for metadata
- **Not Queryable:** Cannot use annotations in selectors
- **Arbitrary Data:** Can store any string (including JSON)
- **Tool Usage:** Used by tools, libraries, and extensions

#### Annotations vs Labels

| Aspect | Labels | Annotations |
|--------|--------|-------------|
| **Purpose** | Identification and selection | Metadata storage |
| **Queryable** | ✅ Yes (with selectors) | ❌ No |
| **Used for Selection** | ✅ Yes | ❌ No |
| **Data Type** | String values | String values (can be JSON) |
| **Length** | Max 63 chars | No limit |
| **Use Case** | Organization, selection | Metadata, tool integration |

#### Annotation Syntax

**Annotation Format:**
- **Key:** Similar to labels (alphanumeric, hyphens, dots)
- **Value:** Any string (can include JSON, URLs, etc.)
- **No Length Limit:** Unlike labels
- **Prefix:** Optional prefix (e.g., `kubernetes.io/`)

**Valid Examples:**
```yaml
annotations:
  description: "Web server pod"
  contact: "team@example.com"
  build-version: "1.2.3"
  deployment.kubernetes.io/revision: "1"
  kubectl.kubernetes.io/last-applied-configuration: '{"apiVersion":"v1"...}'
```

#### Adding Annotations

**Method 1: In YAML**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  annotations:
    description: "Web server pod for production"
    contact: "web-team@example.com"
    build-version: "1.2.3"
    deployment-date: "2024-01-15"
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

**Method 2: Using kubectl**

```bash
# Add annotation
kubectl annotate pod nginx-pod description="Web server pod"

# Add multiple annotations
kubectl annotate pod nginx-pod \
  description="Web server pod" \
  contact="team@example.com"

# Overwrite existing annotation
kubectl annotate pod nginx-pod description="Updated description" --overwrite

# Remove annotation
kubectl annotate pod nginx-pod description-
```

#### Viewing Annotations

```bash
# View annotations
kubectl get pod nginx-pod -o jsonpath='{.metadata.annotations}'

# View specific annotation
kubectl get pod nginx-pod -o jsonpath='{.metadata.annotations.description}'

# View with describe
kubectl describe pod nginx-pod

# View in YAML
kubectl get pod nginx-pod -o yaml
```

#### Common Annotations

**Kubernetes System Annotations:**

```yaml
annotations:
  # Deployment revision
  deployment.kubernetes.io/revision: "2"
  
  # Last applied configuration
  kubectl.kubernetes.io/last-applied-configuration: '{"apiVersion":"v1"...}'
  
  # Pod disruption budget
  pod-disruption-budget.kubernetes.io/name: "my-pdb"
```

**Custom Annotations:**

```yaml
annotations:
  # Build information
  build-version: "1.2.3"
  build-date: "2024-01-15"
  git-commit: "abc1234"
  
  # Contact information
  contact: "team@example.com"
  owner: "web-team"
  
  # Documentation
  description: "Web server pod"
  documentation: "https://docs.example.com/nginx"
  
  # Tool integration
  prometheus.io/scrape: "true"
  prometheus.io/port: "8080"
```

#### Annotation Use Cases

**1. Build and Version Information**

```yaml
annotations:
  build-version: "1.2.3"
  build-date: "2024-01-15T10:00:00Z"
  git-commit: "abc1234"
  git-branch: "main"
```

**2. Contact and Ownership**

```yaml
annotations:
  contact: "team@example.com"
  owner: "web-team"
  slack-channel: "#web-team"
```

**3. Tool Integration**

```yaml
annotations:
  # Prometheus scraping
  prometheus.io/scrape: "true"
  prometheus.io/port: "8080"
  prometheus.io/path: "/metrics"
  
  # Linkerd
  linkerd.io/inject: enabled
  
  # Istio
  sidecar.istio.io/inject: "true"
```

**4. Documentation**

```yaml
annotations:
  description: "Web server pod for production environment"
  documentation: "https://docs.example.com/nginx"
  runbook: "https://runbook.example.com/nginx"
```

**5. Deployment Information**

```yaml
annotations:
  deployment-date: "2024-01-15"
  deployed-by: "ci-cd-pipeline"
  deployment-reason: "Rolling update"
```

**6. JSON Data**

```yaml
annotations:
  config: '{"database":"mysql","port":3306}'
  metadata: '{"team":"web","environment":"production"}'
```

#### Annotations in Different Objects

**Pod Annotations:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    description: "Web server pod"
    build-version: "1.2.3"
```

**Deployment Annotations:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "2"
    description: "Web server deployment"
```

**Service Annotations:**

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"
    description: "Load balancer service"
```

#### Annotation Best Practices

**1. Use for Metadata, Not Selection**

```yaml
# ✅ Good - Use annotations for metadata
annotations:
  description: "Web server pod"
  contact: "team@example.com"

# ❌ Wrong - Use labels for selection
annotations:
  app: nginx  # Should be in labels
```

**2. Use Standard Prefixes**

```yaml
annotations:
  # Kubernetes standard
  deployment.kubernetes.io/revision: "2"
  
  # Tool-specific
  prometheus.io/scrape: "true"
  
  # Custom with prefix
  mycompany.com/team: "web"
```

**3. Store Structured Data as JSON**

```yaml
annotations:
  config: '{"database":"mysql","port":3306}'
  metadata: '{"team":"web","env":"prod"}'
```

**4. Document Important Information**

```yaml
annotations:
  description: "Web server pod"
  documentation: "https://docs.example.com"
  runbook: "https://runbook.example.com"
```

**5. Don't Store Sensitive Data**

```yaml
# ❌ Bad - Don't store secrets
annotations:
  password: "secret123"  # Use Secrets instead

# ✅ Good - Store non-sensitive metadata
annotations:
  description: "Web server pod"
```

#### Annotations Commands Summary

```bash
# Add annotation
kubectl annotate <resource> <name> <key>=<value>

# Add multiple annotations
kubectl annotate <resource> <name> <key1>=<value1> <key2>=<value2>

# Overwrite annotation
kubectl annotate <resource> <name> <key>=<value> --overwrite

# Remove annotation
kubectl annotate <resource> <name> <key>-

# View annotations
kubectl get <resource> <name> -o jsonpath='{.metadata.annotations}'

# View specific annotation
kubectl get <resource> <name> -o jsonpath='{.metadata.annotations.<key>}'
```

#### Practical Examples

**Example 1: Add Build Information**

```bash
# Add build annotations to pod
kubectl annotate pod nginx-pod \
  build-version="1.2.3" \
  build-date="2024-01-15" \
  git-commit="abc1234"
```

**Example 2: Add Tool Integration**

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    prometheus.io/scrape: "true"
    prometheus.io/port: "8080"
    prometheus.io/path: "/metrics"
```

**Example 3: Add Documentation**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    description: "Web server deployment"
    documentation: "https://docs.example.com/nginx"
    contact: "web-team@example.com"
```

#### Key Takeaways

1. **Annotations store metadata** - Not used for selection
2. **Not queryable** - Cannot use in selectors
3. **Arbitrary data** - Can store any string (including JSON)
4. **No length limit** - Unlike labels (63 chars)
5. **Tool integration** - Used by monitoring, service mesh, etc.
6. **Use for metadata** - Description, contact, build info
7. **Don't store secrets** - Use Secrets for sensitive data
8. **Use standard prefixes** - For tool integration

Annotations complement labels by providing a way to store additional metadata that doesn't need to be queryable or used for selection.

---

### ReplicaSet

**ReplicaSet** ensures that a specified number of pod replicas are running at any given time. It's a lower-level concept that is typically managed by Deployments.

#### What is a ReplicaSet?

A ReplicaSet is a Kubernetes object that:
- Maintains a stable set of pod replicas
- Ensures desired number of pods are running
- Replaces pods that fail or are deleted
- Uses label selectors to identify pods

**Key Characteristics:**
- **Replica Management:** Maintains desired number of replicas
- **Self-Healing:** Automatically replaces failed pods
- **Label-Based:** Uses label selectors to manage pods
- **Lower-Level:** Usually managed by Deployments

#### ReplicaSet Diagram

```mermaid
graph TB
    RS[ReplicaSet<br/>Desired: 3 Replicas]
    
    RS -->|Manages| Pod1[Pod 1<br/>app=nginx]
    RS -->|Manages| Pod2[Pod 2<br/>app=nginx]
    RS -->|Manages| Pod3[Pod 3<br/>app=nginx]
    
    Pod1 -.->|Fails| RS
    RS -->|Creates| Pod4[Pod 4<br/>app=nginx<br/>Replacement]
    
    style RS fill:#326ce5,color:#fff
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
    style Pod3 fill:#f4a261,color:#000
    style Pod4 fill:#90ee90,color:#000
```

#### ReplicaSet Structure

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replicaset
spec:
  replicas: 3                    # Desired number of replicas
  selector:                       # Label selector
    matchLabels:
      app: nginx
  template:                       # Pod template
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
```

#### ReplicaSet Spec Fields Explained

**apiVersion:**
- API version for ReplicaSet
- Required: `apps/v1`

**kind:**
- Object type
- Required: `ReplicaSet`

**metadata:**
- Object metadata (name, namespace, labels, annotations)
- **name:** ReplicaSet name (required)
- **namespace:** Namespace (optional, defaults to default)
- **labels:** Key-value pairs for organization
- **annotations:** Additional metadata

**spec:**
- ReplicaSet specification
- **replicas:** Desired number of pod replicas (default: 1)
- **selector:** Label selector to identify pods
  - **matchLabels:** Equality-based selector (key-value pairs)
  - **matchExpressions:** Set-based selector (operators: In, NotIn, Exists, DoesNotExist)
- **template:** Pod template (required)
  - **metadata:** Pod metadata (labels must match selector)
  - **spec:** Pod specification (containers, volumes, etc.)

**Important Notes:**
- Template labels **must match** selector labels
- ReplicaSet manages pods matching the selector
- If pods exist with matching labels, ReplicaSet adopts them

#### Creating ReplicaSet

**Method 1: Using YAML**

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  namespace: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

```bash
kubectl apply -f replicaset.yaml
```

**Method 2: Using kubectl**

```bash
# Create ReplicaSet imperatively
kubectl create replicaset nginx-rs --image=nginx:latest --replicas=3
```

#### Managing ReplicaSet

```bash
# List ReplicaSets
kubectl get replicasets
kubectl get rs

# Get ReplicaSet details
kubectl get rs <replicaset-name>

# Describe ReplicaSet
kubectl describe rs <replicaset-name>

# Scale ReplicaSet
kubectl scale rs <replicaset-name> --replicas=5

# Delete ReplicaSet (pods are also deleted)
kubectl delete rs <replicaset-name>

# Delete ReplicaSet but keep pods
kubectl delete rs <replicaset-name> --cascade=orphan
```

#### How ReplicaSet Works

1. **ReplicaSet watches for pods** matching its selector
2. **If pods < desired:** Creates new pods
3. **If pods > desired:** Deletes excess pods
4. **If pod fails:** ReplicaSet creates replacement
5. **Continuous reconciliation:** Maintains desired state

#### ReplicaSet Selector

**Label Selector:**
```yaml
selector:
  matchLabels:
    app: nginx
    env: production
```

**Match Expressions:**
```yaml
selector:
  matchExpressions:
  - key: app
    operator: In
    values:
    - nginx
    - apache
```

#### ReplicaSet vs Manual Pod Management

**Without ReplicaSet:**
- Pod fails → Manual intervention needed
- Need to manually create replacement
- No automatic scaling

**With ReplicaSet:**
- Pod fails → Automatically replaced
- Maintains desired count
- Self-healing

#### ReplicaSet Best Practices

1. **Don't create ReplicaSets directly** - Use Deployments instead
2. **Match labels correctly** - Template labels must match selector
3. **Use meaningful labels** - For better organization
4. **Set appropriate replicas** - Based on workload requirements

#### Example Reference

For a practical example of a ReplicaSet YAML file, check out:

- **[nginx/replicaset.yml](./nginx/replicaset.yml)** - Example ReplicaSet definition

This example demonstrates:
- ReplicaSet structure with replicas
- Label selector configuration
- Pod template definition
- Basic ReplicaSet pattern

**To use this example:**
```bash
# Apply the ReplicaSet
kubectl apply -f nginx/replicaset.yml

# View the ReplicaSet
kubectl get rs -n nginx

# View pods created by ReplicaSet
kubectl get pods -n nginx -l app=nginx

# Scale the ReplicaSet
kubectl scale rs nginx-replicaset --replicas=5 -n nginx
```

#### Key Takeaways

1. **ReplicaSet maintains pod replicas** - Ensures desired count
2. **Self-healing** - Automatically replaces failed pods
3. **Label-based selection** - Uses selectors to identify pods
4. **Usually managed by Deployments** - Don't create directly
5. **Template must match selector** - Labels in template must match selector

---

### Deployments

**Deployment** is a higher-level concept that manages ReplicaSets and provides declarative updates for Pods. It's the recommended way to manage stateless applications in Kubernetes.

#### What is a Deployment?

A Deployment provides:
- **Declarative updates** for Pods and ReplicaSets
- **Rolling updates** and rollbacks
- **Replica management** (via ReplicaSet)
- **Self-healing** capabilities

**Key Characteristics:**
- **Manages ReplicaSets:** Creates and manages ReplicaSets
- **Rolling Updates:** Updates pods with zero downtime
- **Rollback:** Can rollback to previous versions
- **History:** Maintains revision history
- **Recommended:** Preferred way to manage stateless apps

#### Deployment Diagram

```mermaid
graph TB
    Dep[Deployment<br/>nginx:1.20<br/>Replicas: 3]
    
    Dep -->|Manages| RS1[ReplicaSet v1<br/>nginx:1.20<br/>3 Pods]
    Dep -->|Updates to| RS2[ReplicaSet v2<br/>nginx:1.21<br/>3 Pods]
    
    RS1 -->|Creates| Pod1[Pod 1]
    RS1 -->|Creates| Pod2[Pod 2]
    RS1 -->|Creates| Pod3[Pod 3]
    
    RS2 -->|Creates| Pod4[Pod 4]
    RS2 -->|Creates| Pod5[Pod 5]
    RS2 -->|Creates| Pod6[Pod 6]
    
    Dep -.->|Rolling Update| RS1
    Dep -.->|Rollback| RS1
    
    style Dep fill:#326ce5,color:#fff
    style RS1 fill:#4fc3f7,color:#000
    style RS2 fill:#90caf9,color:#000
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
    style Pod3 fill:#f4a261,color:#000
    style Pod4 fill:#90ee90,color:#000
    style Pod5 fill:#90ee90,color:#000
    style Pod6 fill:#90ee90,color:#000
```

#### Deployment Structure

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
```

#### Deployment Spec Fields Explained

**apiVersion:**
- API version for Deployment
- Required: `apps/v1`

**kind:**
- Object type
- Required: `Deployment`

**metadata:**
- Object metadata (name, namespace, labels, annotations)
- **name:** Deployment name (required)
- **namespace:** Namespace (optional, defaults to default)
- **labels:** Key-value pairs for organization
- **annotations:** Additional metadata

**spec:**
- Deployment specification
- **replicas:** Desired number of pod replicas (default: 1)
- **selector:** Label selector to identify pods
  - **matchLabels:** Equality-based selector (key-value pairs)
  - **matchExpressions:** Set-based selector (operators: In, NotIn, Exists, DoesNotExist)
- **template:** Pod template (required)
  - **metadata:** Pod metadata (labels must match selector)
  - **spec:** Pod specification (containers, volumes, etc.)
- **strategy:** Update strategy (optional)
  - **type:** `RollingUpdate` (default) or `Recreate`
  - **rollingUpdate:** Rolling update configuration
    - **maxSurge:** Maximum number of pods that can be created above desired (default: 25%)
    - **maxUnavailable:** Maximum number of pods that can be unavailable (default: 25%)
- **revisionHistoryLimit:** Number of old ReplicaSets to retain (default: 10)
- **progressDeadlineSeconds:** Maximum time to wait for Deployment to progress (default: 600s)
- **minReadySeconds:** Minimum seconds a pod must be ready (default: 0)

**Important Notes:**
- Template labels **must match** selector labels
- Deployment creates and manages ReplicaSets
- Each update creates a new ReplicaSet
- Old ReplicaSets are kept for rollback

#### Creating Deployments

**Method 1: Using YAML (Recommended)**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

```bash
kubectl apply -f deployment.yaml
```

**Method 2: Using kubectl**

```bash
# Create deployment
kubectl create deployment nginx --image=nginx:latest

# Create with replicas
kubectl create deployment nginx --image=nginx:latest --replicas=3
```

#### Managing Deployments

```bash
# List deployments
kubectl get deployments
kubectl get deploy

# Get deployment details
kubectl get deployment <deployment-name>

# Describe deployment
kubectl describe deployment <deployment-name>

# Scale deployment
kubectl scale deployment <deployment-name> --replicas=5

# Update deployment (change image)
kubectl set image deployment/<deployment-name> nginx=nginx:1.21

# Rollout status
kubectl rollout status deployment/<deployment-name>

# Rollout history
kubectl rollout history deployment/<deployment-name>

# Rollback to previous version
kubectl rollout undo deployment/<deployment-name>

# Rollback to specific revision
kubectl rollout undo deployment/<deployment-name> --to-revision=2

# Pause rollout
kubectl rollout pause deployment/<deployment-name>

# Resume rollout
kubectl rollout resume deployment/<deployment-name>

# Delete deployment
kubectl delete deployment <deployment-name>
```

#### Deployment Update Strategies

**Rolling Update (Default):**

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1           # Can have 1 extra pod during update
      maxUnavailable: 0    # No pods unavailable during update
```

**Recreate:**

```yaml
spec:
  strategy:
    type: Recreate         # Terminates old pods before creating new ones
```

#### Rolling Update Process

```
Current State: 3 pods running nginx:1.20
    ↓
Update to nginx:1.21
    ↓
1. Create new ReplicaSet with nginx:1.21
2. Scale up new ReplicaSet (1 pod)
3. Scale down old ReplicaSet (1 pod)
4. Continue until all pods updated
    ↓
Final State: 3 pods running nginx:1.21
```

#### Deployment Rollback

```bash
# View rollout history
kubectl rollout history deployment/nginx-deployment

# Rollback to previous version
kubectl rollout undo deployment/nginx-deployment

# Rollback to specific revision
kubectl rollout undo deployment/nginx-deployment --to-revision=2

# View specific revision
kubectl rollout history deployment/nginx-deployment --revision=2
```

#### Deployment Status

```bash
# Check deployment status
kubectl get deployment <deployment-name>

# Output shows:
# NAME               READY   UP-TO-DATE   AVAILABLE   AGE
# nginx-deployment   3/3     3            3           5m

# READY: Current/Desired replicas
# UP-TO-DATE: Replicas updated to latest
# AVAILABLE: Replicas available for traffic
```

#### Deployment Conditions

Deployments have conditions:
- **Available:** Deployment has minimum availability
- **Progressing:** Deployment is progressing
- **ReplicaFailure:** ReplicaSet creation failed

#### Deployment Best Practices

1. **Use Deployments for stateless apps** - Not for stateful applications
2. **Set resource limits** - In pod template
3. **Use health checks** - Liveness and readiness probes
4. **Use specific image tags** - Not `latest`
5. **Set update strategy** - RollingUpdate or Recreate
6. **Use labels consistently** - For selection and organization
7. **Monitor rollout status** - Check during updates

#### Example Reference

For a practical example of a Deployment YAML file, check out:

- **[nginx/deployment.yml](./nginx/deployment.yml)** - Example Deployment definition

This example demonstrates:
- Deployment structure with replicas
- Label selector configuration
- Pod template definition
- Basic Deployment pattern

**To use this example:**
```bash
# Apply the Deployment
kubectl apply -f nginx/deployment.yml

# View the Deployment
kubectl get deployment -n nginx

# View pods created by Deployment
kubectl get pods -n nginx -l app=nginx

# Scale the Deployment
kubectl scale deployment nginx-deployment --replicas=5 -n nginx

# Update the image
kubectl set image deployment/nginx-deployment nginx=nginx:1.21 -n nginx

# Check rollout status
kubectl rollout status deployment/nginx-deployment -n nginx
```

#### Key Takeaways

1. **Deployments manage ReplicaSets** - Higher-level abstraction
2. **Rolling updates** - Zero-downtime updates
3. **Rollback capability** - Can revert to previous versions
4. **Recommended for stateless apps** - Standard way to deploy
5. **Self-healing** - Automatically replaces failed pods
6. **Revision history** - Tracks changes for rollback

---

### StatefulSet

**StatefulSet** manages stateful applications and provides guarantees about the ordering and uniqueness of Pods. Unlike Deployments, StatefulSets maintain a sticky identity for each Pod.

#### What is a StatefulSet?

A StatefulSet provides:
- **Stable network identity** - Each pod gets stable hostname
- **Stable storage** - Each pod gets persistent storage
- **Ordered deployment** - Pods created in order
- **Ordered scaling** - Pods scaled up/down in order
- **Ordered updates** - Pods updated in reverse order

**Key Characteristics:**
- **Stable Identity:** Pod name and hostname don't change
- **Persistent Storage:** Each pod gets its own volume
- **Ordered Operations:** Deploy, scale, update in order
- **Stateful Apps:** Designed for databases, queues, etc.

#### StatefulSet Diagram

```mermaid
graph TB
    SS[StatefulSet<br/>mysql<br/>Replicas: 3]
    
    SS -->|Creates in Order| Pod0[mysql-0<br/>Stable Identity<br/>Persistent Storage]
    SS -->|Then| Pod1[mysql-1<br/>Stable Identity<br/>Persistent Storage]
    SS -->|Then| Pod2[mysql-2<br/>Stable Identity<br/>Persistent Storage]
    
    Pod0 -->|Knows| Pod1
    Pod1 -->|Knows| Pod2
    
    SS -.->|Scales Down| Pod2
    SS -.->|Then| Pod1
    SS -.->|Then| Pod0
    
    style SS fill:#326ce5,color:#fff
    style Pod0 fill:#f4a261,color:#000
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
```

#### StatefulSet Structure

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql-headless    # Headless service name
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        volumeMounts:
        - name: data
          mountPath: /var/lib/mysql
  volumeClaimTemplates:          # Persistent volume for each pod
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```

#### StatefulSet Spec Fields Explained

**apiVersion:**
- API version for StatefulSet
- Required: `apps/v1`

**kind:**
- Object type
- Required: `StatefulSet`

**metadata:**
- Object metadata (name, namespace, labels, annotations)
- **name:** StatefulSet name (required)
- **namespace:** Namespace (optional, defaults to default)
- **labels:** Key-value pairs for organization
- **annotations:** Additional metadata

**spec:**
- StatefulSet specification
- **serviceName:** Name of headless service (required) - Provides stable network identity
- **replicas:** Desired number of pod replicas (default: 1)
- **selector:** Label selector to identify pods
  - **matchLabels:** Equality-based selector (key-value pairs)
  - **matchExpressions:** Set-based selector (operators: In, NotIn, Exists, DoesNotExist)
- **template:** Pod template (required)
  - **metadata:** Pod metadata (labels must match selector)
  - **spec:** Pod specification (containers, volumes, etc.)
- **volumeClaimTemplates:** Persistent volume claim templates (optional)
  - Creates a PVC for each pod
  - Each pod gets its own persistent volume
  - PVC name format: `<volumeClaimTemplate-name>-<pod-name>`
- **updateStrategy:** Update strategy (optional)
  - **type:** `RollingUpdate` (default) or `OnDelete`
  - **rollingUpdate:** Rolling update configuration
    - **partition:** Pods with index >= partition are updated (default: 0)
- **podManagementPolicy:** Pod management policy (optional)
  - `OrderedReady` (default): Pods created/terminated in order
  - `Parallel`: Pods created/terminated in parallel
- **revisionHistoryLimit:** Number of old ControllerRevisions to retain (default: 10)

**Important Notes:**
- **serviceName is required** - Must reference a headless Service
- Template labels **must match** selector labels
- Pods get stable names: `<statefulset-name>-<ordinal>` (e.g., mysql-0, mysql-1)
- Each pod gets its own PVC from volumeClaimTemplates
- Pods are created/updated/deleted in order

#### Creating StatefulSet

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "password"
        volumeMounts:
        - name: data
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```

#### Managing StatefulSet

```bash
# List StatefulSets
kubectl get statefulsets
kubectl get sts

# Get StatefulSet details
kubectl get sts <statefulset-name>

# Describe StatefulSet
kubectl describe sts <statefulset-name>

# Scale StatefulSet
kubectl scale sts <statefulset-name> --replicas=5

# Update StatefulSet
kubectl set image sts/<statefulset-name> mysql=mysql:8.0.28

# Delete StatefulSet
kubectl delete sts <statefulset-name>

# Delete StatefulSet but keep pods
kubectl delete sts <statefulset-name> --cascade=orphan
```

#### StatefulSet Pod Identity

**Stable Network Identity:**

```bash
# Pods get stable names
mysql-0
mysql-1
mysql-2

# Stable hostname
mysql-0.mysql.default.svc.cluster.local
mysql-1.mysql.default.svc.cluster.local
mysql-2.mysql.default.svc.cluster.local
```

**Stable Storage:**

```bash
# Each pod gets its own PVC
data-mysql-0
data-mysql-1
data-mysql-2
```

#### StatefulSet Ordering

**Deployment Order:**
1. Create mysql-0, wait for Ready
2. Create mysql-1, wait for Ready
3. Create mysql-2, wait for Ready

**Scaling Down Order:**
1. Terminate mysql-2
2. Terminate mysql-1
3. Terminate mysql-0

**Scaling Up Order:**
1. Create mysql-3
2. Create mysql-4
3. etc.

#### Headless Service

StatefulSets require a Headless Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  clusterIP: None              # Headless service
  selector:
    app: mysql
  ports:
  - port: 3306
```

**Why Headless Service?**
- Provides stable network identity
- Each pod gets DNS entry
- Enables direct pod-to-pod communication

#### StatefulSet Update Strategies

**Rolling Update (Default):**

```yaml
spec:
  updateStrategy:
    type: RollingUpdate
    rollingUpdate:
      partition: 2    # Update pods with index >= 2
```

**OnDelete:**

```yaml
spec:
  updateStrategy:
    type: OnDelete    # Update only when pod is deleted
```

#### StatefulSet Use Cases

**Databases:**
- MySQL, PostgreSQL, MongoDB
- Need stable identity and storage
- Ordered operations

**Message Queues:**
- RabbitMQ, Kafka
- Need stable identity
- Ordered scaling

**Distributed Systems:**
- Elasticsearch, Cassandra
- Need stable network identity
- Ordered operations

#### StatefulSet Best Practices

1. **Use for stateful applications** - Databases, queues, etc.
2. **Create Headless Service first** - Required for StatefulSet
3. **Use volumeClaimTemplates** - For persistent storage
4. **Plan scaling carefully** - Ordered operations take time
5. **Use init containers** - For initialization logic
6. **Set resource limits** - In pod template
7. **Use health checks** - Liveness and readiness probes

#### Key Takeaways

1. **StatefulSets for stateful apps** - Databases, queues, etc.
2. **Stable identity** - Pod names and hostnames don't change
3. **Persistent storage** - Each pod gets its own volume
4. **Ordered operations** - Deploy, scale, update in order
5. **Requires Headless Service** - For stable network identity
6. **Slower than Deployments** - Due to ordering requirements

---

### Comparison: ReplicaSet vs Deployment vs StatefulSet

#### Quick Comparison Table

| Feature | ReplicaSet | Deployment | StatefulSet |
|---------|-----------|------------|-------------|
| **Purpose** | Maintain pod replicas | Manage stateless apps | Manage stateful apps |
| **Updates** | Manual | Rolling updates | Ordered updates |
| **Rollback** | ❌ No | ✅ Yes | ✅ Yes |
| **Pod Identity** | Random | Random | Stable |
| **Storage** | Ephemeral | Ephemeral | Persistent |
| **Ordering** | ❌ No | ❌ No | ✅ Yes |
| **Use Case** | Lower-level | Stateless apps | Stateful apps |
| **Service** | Regular/Headless | Regular | Headless required |
| **Scaling** | Instant | Instant | Ordered |
| **Naming** | Random | Random | Sequential (app-0, app-1) |

#### Detailed Differences

**1. Purpose and Abstraction Level**

- **ReplicaSet:** Low-level, maintains pod count
- **Deployment:** High-level, manages ReplicaSets with updates
- **StatefulSet:** High-level, manages stateful applications

**2. Pod Identity**

- **ReplicaSet:** Random pod names (e.g., `nginx-abc123`)
- **Deployment:** Random pod names (e.g., `nginx-deployment-abc123`)
- **StatefulSet:** Stable, sequential names (e.g., `mysql-0`, `mysql-1`)

**3. Storage**

- **ReplicaSet:** Ephemeral storage only
- **Deployment:** Ephemeral storage only
- **StatefulSet:** Persistent storage per pod (via volumeClaimTemplates)

**4. Updates**

- **ReplicaSet:** Manual pod replacement
- **Deployment:** Automatic rolling updates
- **StatefulSet:** Ordered rolling updates

**5. Rollback**

- **ReplicaSet:** ❌ No rollback capability
- **Deployment:** ✅ Rollback to previous revisions
- **StatefulSet:** ✅ Rollback capability

**6. Scaling**

- **ReplicaSet:** Instant, no ordering
- **Deployment:** Instant, no ordering
- **StatefulSet:** Ordered (one at a time)

**7. Network Identity**

- **ReplicaSet:** Random IP, no stable DNS
- **Deployment:** Random IP, no stable DNS
- **StatefulSet:** Stable hostname, stable DNS

**8. Use Cases**

- **ReplicaSet:** Lower-level pod management (usually via Deployment)
- **Deployment:** Web servers, APIs, stateless microservices
- **StatefulSet:** Databases, message queues, distributed systems

#### When to Use What?

**Use ReplicaSet when:**
- ⚠️ **Don't use directly** - Use Deployment instead
- Only if you need very basic pod management
- Lower-level control needed

**Use Deployment when:**
- ✅ **Stateless applications** - Web servers, APIs
- ✅ **Need rolling updates** - Zero-downtime updates
- ✅ **Need rollback** - Ability to revert changes
- ✅ **Most common use case** - Default for stateless apps

**Use StatefulSet when:**
- ✅ **Stateful applications** - Databases, queues
- ✅ **Need stable identity** - Pod names must be stable
- ✅ **Need persistent storage** - Each pod needs its own storage
- ✅ **Need ordered operations** - Deploy/scale in order

#### Relationship Diagram

```mermaid
graph TB
    Dep[Deployment]
    SS[StatefulSet]
    
    Dep -->|Manages| RS[ReplicaSet]
    SS -->|Manages| RS2[ReplicaSet]
    
    RS -->|Creates| Pod1[Pods<br/>Random Names]
    RS2 -->|Creates| Pod2[Pods<br/>Stable Names]
    
    Pod2 -->|Has| PVC[Persistent Volumes]
    
    style Dep fill:#326ce5,color:#fff
    style SS fill:#326ce5,color:#fff
    style RS fill:#4fc3f7,color:#000
    style RS2 fill:#4fc3f7,color:#000
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
    style PVC fill:#90ee90,color:#000
```

#### Summary

- **ReplicaSet:** Basic pod replica management (low-level)
- **Deployment:** Stateless app management with updates (recommended for most cases)
- **StatefulSet:** Stateful app management with stable identity (for databases, queues)

**Best Practice:** Use Deployments for stateless applications and StatefulSets for stateful applications. ReplicaSets are typically managed by Deployments and StatefulSets, not created directly.

---

### DaemonSet

**DaemonSet** ensures that a copy of a Pod runs on all (or specific) nodes in the cluster. It's used for system-level services that need to run on every node.

#### What is a DaemonSet?

A DaemonSet ensures that:
- A copy of a Pod runs on **all nodes** in the cluster
- Or on **selected nodes** (using node selectors)
- Pods are automatically added when new nodes join
- Pods are automatically removed when nodes are removed

**Key Characteristics:**
- **One Pod per Node:** Ensures one pod on each node
- **Node-Specific:** Pods are tied to specific nodes
- **Automatic Management:** Adds/removes pods as nodes join/leave
- **System Services:** Perfect for logging, monitoring, networking

#### DaemonSet Diagram

```mermaid
graph TB
    DS[DaemonSet<br/>fluentd-logging]
    
    Node1[Node 1] -->|Runs| Pod1[fluentd Pod<br/>Node 1]
    Node2[Node 2] -->|Runs| Pod2[fluentd Pod<br/>Node 2]
    Node3[Node 3] -->|Runs| Pod3[fluentd Pod<br/>Node 3]
    Node4[Node 4] -->|Runs| Pod4[fluentd Pod<br/>Node 4]
    
    DS -->|Ensures Pod on| Node1
    DS -->|Ensures Pod on| Node2
    DS -->|Ensures Pod on| Node3
    DS -->|Ensures Pod on| Node4
    
    Node5[New Node 5] -.->|Joins Cluster| DS
    DS -->|Creates| Pod5[fluentd Pod<br/>Node 5]
    
    style DS fill:#326ce5,color:#fff
    style Node1 fill:#e3f2fd,color:#000
    style Node2 fill:#e3f2fd,color:#000
    style Node3 fill:#e3f2fd,color:#000
    style Node4 fill:#e3f2fd,color:#000
    style Node5 fill:#90ee90,color:#000
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
    style Pod3 fill:#f4a261,color:#000
    style Pod4 fill:#f4a261,color:#000
    style Pod5 fill:#90ee90,color:#000
```

#### DaemonSet Structure

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-logging
  template:
    metadata:
      labels:
        name: fluentd-logging
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd:latest
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

#### DaemonSet Spec Fields Explained

**apiVersion:**
- API version for DaemonSet
- Required: `apps/v1`

**kind:**
- Object type
- Required: `DaemonSet`

**metadata:**
- Object metadata (name, namespace, labels, annotations)
- **name:** DaemonSet name (required)
- **namespace:** Namespace (optional, defaults to default)
- **labels:** Key-value pairs for organization
- **annotations:** Additional metadata

**spec:**
- DaemonSet specification
- **selector:** Label selector to identify pods (required)
  - **matchLabels:** Equality-based selector (key-value pairs)
  - **matchExpressions:** Set-based selector (operators: In, NotIn, Exists, DoesNotExist)
- **template:** Pod template (required)
  - **metadata:** Pod metadata (labels must match selector)
  - **spec:** Pod specification (containers, volumes, etc.)
    - Can include **nodeSelector** to run on specific nodes
    - Can include **affinity** for node affinity rules
    - Can include **tolerations** to run on tainted nodes
- **updateStrategy:** Update strategy (optional)
  - **type:** `RollingUpdate` (default) or `OnDelete`
  - **rollingUpdate:** Rolling update configuration
    - **maxUnavailable:** Maximum number of pods unavailable during update (default: 1)
- **revisionHistoryLimit:** Number of old ControllerRevisions to retain (default: 10)
- **minReadySeconds:** Minimum seconds a pod must be ready (default: 0)

**Important Notes:**
- **No replicas field** - Number of pods = number of nodes (or selected nodes)
- Template labels **must match** selector labels
- DaemonSet ensures one pod per node (or per selected node)
- Pods are automatically created when nodes join
- Pods are automatically deleted when nodes are removed
- Use **nodeSelector** or **affinity** to run on specific nodes
- Use **tolerations** to run on tainted nodes (e.g., master nodes)

#### Creating DaemonSet

**Method 1: Using YAML (Recommended)**

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
  namespace: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

```bash
kubectl apply -f daemonset.yaml
```

**Method 2: Using kubectl**

```bash
# Create DaemonSet imperatively
kubectl create daemonset nginx-ds --image=nginx:latest
```

#### Managing DaemonSet

```bash
# List DaemonSets
kubectl get daemonsets
kubectl get ds

# Get DaemonSet details
kubectl get ds <daemonset-name>

# Describe DaemonSet
kubectl describe ds <daemonset-name>

# View pods created by DaemonSet
kubectl get pods -l app=nginx

# Update DaemonSet (change image)
kubectl set image ds/<daemonset-name> nginx=nginx:1.21

# Delete DaemonSet
kubectl delete ds <daemonset-name>

# Delete DaemonSet but keep pods
kubectl delete ds <daemonset-name> --cascade=orphan
```

#### Node Selection

**Run on All Nodes (Default):**

```yaml
spec:
  template:
    spec:
      containers:
      - name: nginx
        image: nginx:latest
```

**Run on Specific Nodes (Node Selector):**

```yaml
spec:
  template:
    spec:
      nodeSelector:
        disktype: ssd
      containers:
      - name: nginx
        image: nginx:latest
```

**Run on Specific Nodes (Node Affinity):**

```yaml
spec:
  template:
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: zone
                operator: In
                values:
                - us-east-1a
      containers:
      - name: nginx
        image: nginx:latest
```

#### DaemonSet Use Cases

**1. Logging Agents**
- Fluentd, Filebeat, Logstash
- Collect logs from all nodes
- Centralized logging

**2. Monitoring Agents**
- Prometheus Node Exporter
- Datadog Agent
- Collect metrics from all nodes

**3. Network Plugins**
- CNI plugins
- Network policy agents
- Run on every node

**4. Storage Daemons**
- GlusterFS, Ceph
- Storage management
- Run on storage nodes

**5. Security Agents**
- Security scanners
- Antivirus agents
- Run on all nodes

#### DaemonSet vs Deployment

| Aspect | DaemonSet | Deployment |
|--------|-----------|------------|
| **Pods per Node** | One pod per node | Fixed number of pods |
| **Scaling** | Automatic (based on nodes) | Manual scaling |
| **Node Selection** | All or selected nodes | Any available node |
| **Use Case** | System services | Application services |
| **Replicas** | Not specified | Specified in spec |

#### DaemonSet Update Strategy

**Rolling Update (Default):**

```yaml
spec:
  updateStrategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1    # Max pods unavailable during update
```

**OnDelete:**

```yaml
spec:
  updateStrategy:
    type: OnDelete    # Update only when pod is deleted manually
```

#### DaemonSet Status

```bash
# Check DaemonSet status
kubectl get ds <daemonset-name>

# Output shows:
# NAME              DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
# fluentd-logging   3         3         3       3            3           <none>          5m

# DESIRED: Number of nodes that should have pod
# CURRENT: Number of pods currently running
# READY: Number of pods ready
# UP-TO-DATE: Number of pods updated to latest
# AVAILABLE: Number of pods available
```

#### DaemonSet Best Practices

1. **Use for system services** - Logging, monitoring, networking
2. **Set resource limits** - Prevent resource exhaustion
3. **Use hostPath volumes carefully** - Security implications
4. **Set node selectors** - Run only on specific nodes when needed
5. **Use tolerations** - Run on tainted nodes if needed
6. **Set update strategy** - RollingUpdate or OnDelete
7. **Use labels consistently** - For organization

#### Tolerations for Tainted Nodes

DaemonSets can run on tainted nodes using tolerations:

```yaml
spec:
  template:
    spec:
      tolerations:
      - key: "node-role.kubernetes.io/master"
        operator: "Exists"
        effect: "NoSchedule"
      containers:
      - name: nginx
        image: nginx:latest
```

#### DaemonSet Example: Logging Agent

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-logging
  namespace: kube-system
spec:
  selector:
    matchLabels:
      name: fluentd-logging
  template:
    metadata:
      labels:
        name: fluentd-logging
    spec:
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd:latest
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

#### DaemonSet Commands Summary

```bash
# Create
kubectl create -f daemonset.yaml
kubectl create daemonset <name> --image=<image>

# Get
kubectl get daemonsets
kubectl get ds
kubectl describe ds <name>

# Update
kubectl set image ds/<name> <container>=<image>
kubectl edit ds <name>

# Delete
kubectl delete ds <name>
kubectl delete -f daemonset.yaml
```

#### Example Reference

For a practical example of a DaemonSet YAML file, check out:

- **[nginx/daemonset.yml](./nginx/daemonset.yml)** - Example DaemonSet definition

This example demonstrates:
- DaemonSet structure with selector
- Pod template definition
- Basic DaemonSet pattern for nginx
- Namespace configuration

**To use this example:**
```bash
# Apply the DaemonSet
kubectl apply -f nginx/daemonset.yml

# View the DaemonSet
kubectl get ds -n nginx

# View pods created by DaemonSet (one per node)
kubectl get pods -n nginx -l app=nginx

# Check which nodes have the pod
kubectl get pods -n nginx -l app=nginx -o wide

# Update the DaemonSet
kubectl set image ds/nginx-daemonset nginx=nginx:1.21 -n nginx

# Check DaemonSet status
kubectl get ds nginx-daemonset -n nginx
```

#### Key Takeaways

1. **DaemonSet runs one pod per node** - Ensures pod on all/selected nodes
2. **Automatic management** - Adds pods when nodes join, removes when nodes leave
3. **Perfect for system services** - Logging, monitoring, networking agents
4. **Node selection** - Can run on all nodes or selected nodes
5. **No replicas field** - Number of pods = number of nodes (or selected nodes)
6. **Use tolerations** - To run on tainted nodes (like master nodes)
7. **Update strategies** - RollingUpdate or OnDelete
8. **HostPath volumes** - Common for accessing node filesystem

DaemonSets are essential for running system-level services that need to be present on every node in your cluster.

---

### Jobs

**Jobs** create one or more Pods and ensure that a specified number of them successfully terminate. Jobs are used for batch processing, one-time tasks, and workloads that need to run to completion.

#### What is a Job?

A Job creates Pods that run until completion (success or failure). Unlike Deployments, Jobs are designed for:
- **One-time tasks** - Tasks that run once and complete
- **Batch processing** - Processing data in batches
- **Workloads that must complete** - Tasks that need to finish successfully

**Key Characteristics:**
- **Runs to completion** - Pods run until they succeed or fail
- **Retry on failure** - Can retry failed pods
- **Parallel execution** - Can run multiple pods in parallel
- **Completion tracking** - Tracks successful completions

#### Job Diagram

```mermaid
graph TB
    Job[Job<br/>completions: 3<br/>parallelism: 2]
    
    Job -->|Creates| Pod1[Pod 1<br/>Running]
    Job -->|Creates| Pod2[Pod 2<br/>Running]
    
    Pod1 -->|Completes| Success1[Success]
    Pod2 -->|Completes| Success2[Success]
    
    Job -->|Creates| Pod3[Pod 3<br/>Running]
    Pod3 -->|Completes| Success3[Success]
    
    Success1 -->|All Complete| JobComplete[Job Complete<br/>3/3 Successful]
    Success2 -->|All Complete| JobComplete
    Success3 -->|All Complete| JobComplete
    
    style Job fill:#326ce5,color:#fff
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
    style Pod3 fill:#f4a261,color:#000
    style Success1 fill:#90ee90,color:#000
    style Success2 fill:#90ee90,color:#000
    style Success3 fill:#90ee90,color:#000
    style JobComplete fill:#4caf50,color:#fff
```

#### Job YAML Structure

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
  namespace: default
spec:
  completions: 1          # Number of successful completions needed
  parallelism: 1         # Number of pods to run in parallel
  backoffLimit: 6        # Number of retries before marking as failed
  activeDeadlineSeconds: 300  # Maximum time job can run (seconds)
  template:
    metadata:
      labels:
        app: batch-task
    spec:
      restartPolicy: Never  # Never, OnFailure, Always
      containers:
      - name: task-container
        image: busybox:latest
        command: ["sh", "-c", "echo Hello World && sleep 10"]
```

#### Job Spec Fields Explained

**completions:**
- Number of successful completions required
- Job completes when this many pods succeed
- Default: 1

**parallelism:**
- Number of pods to run in parallel
- Can be less than or equal to completions
- Default: 1

**backoffLimit:**
- Number of retries before marking job as failed
- Default: 6

**activeDeadlineSeconds:**
- Maximum time (seconds) the job can run
- Job is terminated if exceeded
- Optional

**restartPolicy:**
- **Never:** Don't restart (default for Jobs)
- **OnFailure:** Restart only on failure
- **Always:** Always restart (not recommended for Jobs)

#### Creating Jobs

**Method 1: Using YAML (Recommended)**

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: demo-job
  namespace: nginx
spec:
  completions: 1
  parallelism: 1
  template:
    metadata:
      labels:
        app: batch-task
    spec:
      containers:
      - name: batch-container
        image: busybox:latest
        command: ["sh", "-c", "echo Hello Dosto! && sleep 10"]
      restartPolicy: Never
```

```bash
kubectl apply -f job.yaml
```

**Method 2: Using kubectl**

```bash
# Create job imperatively
kubectl create job my-job --image=busybox -- echo "Hello World"

# Create job with command
kubectl create job my-job --image=busybox -- sh -c "echo Hello && sleep 10"
```

#### Managing Jobs

```bash
# List jobs
kubectl get jobs
kubectl get job

# Get job details
kubectl get job <job-name>

# Describe job
kubectl describe job <job-name>

# View pods created by job
kubectl get pods -l job-name=<job-name>

# View job logs
kubectl logs job/<job-name>

# Delete job (pods are also deleted)
kubectl delete job <job-name>

# Delete job but keep pods
kubectl delete job <job-name> --cascade=orphan
```

#### Job Types

**1. Non-Parallel Job (completions: 1, parallelism: 1)**
- Runs one pod until completion
- Simplest job type

```yaml
spec:
  completions: 1
  parallelism: 1
```

**2. Fixed Completion Count Job (completions: N, parallelism: 1)**
- Runs N pods sequentially
- Each pod must complete successfully

```yaml
spec:
  completions: 5
  parallelism: 1
```

**3. Parallel Job with Fixed Completion (completions: N, parallelism: M)**
- Runs M pods in parallel
- Continues until N completions

```yaml
spec:
  completions: 10
  parallelism: 3
```

**4. Parallel Job with Work Queue (completions: null, parallelism: M)**
- Runs M pods in parallel
- Continues until all work is done
- Pods coordinate via work queue

```yaml
spec:
  completions: null
  parallelism: 3
```

#### Job Status

```bash
# Check job status
kubectl get job <job-name>

# Output shows:
# NAME      COMPLETIONS   DURATION   AGE
# my-job    1/1           30s         5m

# COMPLETIONS: Successful/Required
# DURATION: Time taken to complete
```

**Job Conditions:**
- **Complete:** Job completed successfully
- **Failed:** Job failed (exceeded backoffLimit)

#### Job Use Cases

**1. Batch Processing**
- Process data files
- Generate reports
- Data transformation

**2. One-Time Tasks**
- Database migrations
- Data imports
- Cleanup tasks

**3. Parallel Processing**
- Process multiple items in parallel
- Distributed computations

**4. Work Queue**
- Process items from a queue
- Distributed task processing

#### Job Best Practices

1. **Set restartPolicy to Never or OnFailure** - Jobs should complete, not restart indefinitely
2. **Set appropriate backoffLimit** - Prevent infinite retries
3. **Set activeDeadlineSeconds** - Prevent jobs from running too long
4. **Use completions and parallelism** - Control job execution
5. **Handle errors properly** - Exit with appropriate codes
6. **Clean up completed jobs** - Delete old jobs to save resources

#### Example Reference

For a practical example of a Job YAML file, check out:

- **[nginx/jobs.yml](./nginx/jobs.yml)** - Example Job definition

This example demonstrates:
- Job structure with completions and parallelism
- Pod template with container specification
- Command execution in container
- RestartPolicy configuration
- Basic Job pattern

**To use this example:**
```bash
# Apply the Job
kubectl apply -f nginx/jobs.yml

# View the Job
kubectl get job -n nginx

# View pods created by Job
kubectl get pods -n nginx -l app=batch-task

# View job logs
kubectl logs -n nginx -l app=batch-task

# Check job status
kubectl describe job demo-job -n nginx

# Delete the Job
kubectl delete job demo-job -n nginx
```

#### Key Takeaways

1. **Jobs run to completion** - Pods run until they succeed or fail
2. **Use for batch tasks** - One-time tasks, batch processing
3. **Set restartPolicy** - Never or OnFailure (not Always)
4. **Control parallelism** - Use parallelism to run multiple pods
5. **Track completions** - Use completions to specify required successes
6. **Set backoffLimit** - Prevent infinite retries
7. **Clean up jobs** - Delete completed jobs

---

### CronJobs

**CronJob** creates Jobs on a time-based schedule. It's like a cron job in Linux, but for Kubernetes Pods. CronJobs are used for periodic tasks, scheduled backups, and recurring batch jobs.

#### What is a CronJob?

A CronJob creates Jobs on a schedule defined by a cron expression. It:
- **Runs on schedule** - Executes jobs at specified times
- **Creates Job objects** - Each execution creates a new Job
- **Maintains history** - Keeps record of successful and failed jobs
- **Time-based execution** - Uses cron syntax for scheduling

**Key Characteristics:**
- **Scheduled execution** - Runs jobs on a schedule
- **Creates Jobs** - Each run creates a new Job
- **History management** - Maintains job history
- **Time zones** - Can specify timezone

#### CronJob Diagram

```mermaid
graph TB
    CJ[CronJob<br/>Schedule: 0 * * * *<br/>Every Hour]
    
    Time1[00:00] -->|Creates| Job1[Job 1<br/>Runs Pods]
    Time2[01:00] -->|Creates| Job2[Job 2<br/>Runs Pods]
    Time3[02:00] -->|Creates| Job3[Job 3<br/>Runs Pods]
    
    Job1 -->|Completes| Success1[Success]
    Job2 -->|Completes| Success2[Success]
    Job3 -->|Completes| Success3[Success]
    
    CJ -->|Manages| Job1
    CJ -->|Manages| Job2
    CJ -->|Manages| Job3
    
    style CJ fill:#326ce5,color:#fff
    style Job1 fill:#f4a261,color:#000
    style Job2 fill:#f4a261,color:#000
    style Job3 fill:#f4a261,color:#000
    style Success1 fill:#90ee90,color:#000
    style Success2 fill:#90ee90,color:#000
    style Success3 fill:#90ee90,color:#000
```

#### CronJob YAML Structure

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: my-cronjob
  namespace: default
spec:
  schedule: "0 * * * *"        # Cron schedule
  timeZone: "America/New_York" # Optional timezone
  startingDeadlineSeconds: 200 # Optional deadline
  concurrencyPolicy: Allow      # Allow, Forbid, Replace
  successfulJobsHistoryLimit: 3 # Keep 3 successful jobs
  failedJobsHistoryLimit: 1     # Keep 1 failed job
  suspend: false                # Set to true to suspend
  jobTemplate:
    spec:
      completions: 1
      parallelism: 1
      template:
        metadata:
          labels:
            app: scheduled-task
        spec:
          restartPolicy: OnFailure
          containers:
          - name: task-container
            image: busybox:latest
            command: ["sh", "-c", "echo Scheduled task && date"]
```

#### Cron Schedule Format

**Cron Expression:**
```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6) (Sunday to Saturday)
│ │ │ │ │
* * * * *
```

**Common Examples:**

```yaml
# Every minute
schedule: "* * * * *"

# Every hour (at minute 0)
schedule: "0 * * * *"

# Every day at midnight
schedule: "0 0 * * *"

# Every day at 2:30 AM
schedule: "30 2 * * *"

# Every Monday at 9:00 AM
schedule: "0 9 * * 1"

# Every first day of month at midnight
schedule: "0 0 1 * *"

# Every 15 minutes
schedule: "*/15 * * * *"

# Every 5 minutes
schedule: "*/5 * * * *"

# Every weekday at 9 AM
schedule: "0 9 * * 1-5"
```

#### CronJob Spec Fields Explained

**schedule:**
- Cron expression defining when to run
- Required field
- Format: `minute hour day month day-of-week`

**timeZone:**
- Timezone for the schedule
- Optional (defaults to kube-controller-manager timezone)
- Example: `"America/New_York"`, `"UTC"`

**startingDeadlineSeconds:**
- Deadline for starting the job
- If missed, job is skipped
- Optional

**concurrencyPolicy:**
- **Allow:** Allow concurrent jobs (default)
- **Forbid:** Don't allow concurrent jobs
- **Replace:** Replace currently running job

**successfulJobsHistoryLimit:**
- Number of successful jobs to keep
- Default: 3
- Set to 0 to not keep any

**failedJobsHistoryLimit:**
- Number of failed jobs to keep
- Default: 1
- Set to 0 to not keep any

**suspend:**
- Set to true to suspend the CronJob
- Default: false
- Jobs won't be created when suspended

**jobTemplate:**
- Template for creating Jobs
- Same structure as Job spec

#### Creating CronJobs

**Method 1: Using YAML (Recommended)**

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: minute-backup
  namespace: nginx
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        metadata:
          labels:
            app: minute-backup
        spec:
          containers:
          - name: backup-container
            image: busybox
            command:
            - sh
            - -c
            - >
              echo "Backup Started" ;
              mkdir -p /backups &&
              mkdir -p /demo-data &&
              cp -r /demo-data /backups &&
              echo "Backup Completed" ;
            volumeMounts:
            - name: data-volume
              mountPath: /demo-data
            - name: backup-volume
              mountPath: /backups
          restartPolicy: OnFailure
          volumes:
          - name: data-volume
            hostPath:
              path: /demo-data
              type: DirectoryOrCreate
          - name: backup-volume
            hostPath:
              path: /backups
              type: DirectoryOrCreate
```

```bash
kubectl apply -f cronjob.yaml
```

**Method 2: Using kubectl**

```bash
# Create CronJob imperatively
kubectl create cronjob my-cronjob --image=busybox --schedule="0 * * * *" -- echo "Hello"
```

#### Managing CronJobs

```bash
# List CronJobs
kubectl get cronjobs
kubectl get cj

# Get CronJob details
kubectl get cronjob <cronjob-name>

# Describe CronJob
kubectl describe cronjob <cronjob-name>

# View jobs created by CronJob
kubectl get jobs -l app=<label>

# Suspend CronJob
kubectl patch cronjob <cronjob-name> -p '{"spec":{"suspend":true}}'

# Resume CronJob
kubectl patch cronjob <cronjob-name> -p '{"spec":{"suspend":false}}'

# Delete CronJob
kubectl delete cronjob <cronjob-name>
```

#### CronJob Concurrency Policies

**Allow (Default):**
```yaml
spec:
  concurrencyPolicy: Allow
```
- Allows multiple jobs to run concurrently
- Use when jobs are independent

**Forbid:**
```yaml
spec:
  concurrencyPolicy: Forbid
```
- Prevents new job if previous is still running
- Use when jobs must not overlap

**Replace:**
```yaml
spec:
  concurrencyPolicy: Replace
```
- Replaces currently running job with new one
- Use when only latest job matters

#### CronJob Use Cases

**1. Scheduled Backups**
- Database backups
- File system backups
- Configuration backups

**2. Periodic Cleanup**
- Clean old logs
- Remove temporary files
- Archive old data

**3. Scheduled Reports**
- Daily reports
- Weekly summaries
- Monthly analytics

**4. Health Checks**
- Periodic health checks
- System monitoring
- Status updates

**5. Data Synchronization**
- Sync data between systems
- Update caches
- Refresh data

#### CronJob Best Practices

1. **Use appropriate schedule** - Don't schedule too frequently
2. **Set concurrencyPolicy** - Choose based on job requirements
3. **Set history limits** - Clean up old jobs
4. **Handle timezones** - Specify timeZone if needed
5. **Set startingDeadlineSeconds** - Handle missed schedules
6. **Test schedules** - Verify cron expressions
7. **Monitor jobs** - Check job success/failure
8. **Use suspend** - Temporarily disable without deleting

#### CronJob Status

```bash
# Check CronJob status
kubectl get cronjob <cronjob-name>

# Output shows:
# NAME           SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
# minute-backup   * * * * *     False     0        30s ago         5m

# SCHEDULE: Cron expression
# SUSPEND: Whether suspended
# ACTIVE: Number of active jobs
# LAST SCHEDULE: When last job was created
```

#### Example Reference

For a practical example of a CronJob YAML file, check out:

- **[nginx/cron-job.yaml](./nginx/cron-job.yaml)** - Example CronJob definition

This example demonstrates:
- CronJob structure with schedule
- Job template specification
- Container with command execution
- Volume mounts for data and backups
- HostPath volumes configuration
- RestartPolicy for job containers
- Complete backup task example

**To use this example:**
```bash
# Apply the CronJob
kubectl apply -f nginx/cron-job.yaml

# View the CronJob
kubectl get cronjob -n nginx

# View jobs created by CronJob
kubectl get jobs -n nginx -l app=minute-backup

# View pods created by jobs
kubectl get pods -n nginx -l app=minute-backup

# View logs from latest job
kubectl logs -n nginx -l app=minute-backup --tail=50

# Suspend the CronJob
kubectl patch cronjob minute-backup -n nginx -p '{"spec":{"suspend":true}}'

# Resume the CronJob
kubectl patch cronjob minute-backup -n nginx -p '{"spec":{"suspend":false}}'

# Check CronJob status
kubectl describe cronjob minute-backup -n nginx

# Delete the CronJob
kubectl delete cronjob minute-backup -n nginx
```

#### Key Takeaways

1. **CronJobs create Jobs on schedule** - Time-based job execution
2. **Use cron syntax** - Standard cron expression format
3. **Each run creates a Job** - CronJob manages Job creation
4. **Set concurrencyPolicy** - Control concurrent executions
5. **Manage history** - Set limits for successful/failed jobs
6. **Use suspend** - Temporarily disable without deletion
7. **Specify timezone** - For accurate scheduling
8. **Monitor execution** - Check job success/failure

CronJobs are essential for running periodic tasks, scheduled backups, and recurring batch jobs in Kubernetes.

---

### Comparison: Jobs vs CronJobs

| Aspect | Job | CronJob |
|--------|-----|---------|
| **Execution** | One-time | Scheduled (recurring) |
| **Trigger** | Manual creation | Time-based schedule |
| **Use Case** | Batch tasks, one-time | Periodic tasks, backups |
| **Creates** | Pods directly | Creates Jobs |
| **Schedule** | N/A | Cron expression |
| **History** | Manual cleanup | Automatic history management |
| **Concurrency** | Controlled by parallelism | Controlled by concurrencyPolicy |

**When to Use:**
- **Job:** One-time tasks, batch processing, manual execution
- **CronJob:** Scheduled tasks, periodic backups, recurring jobs

</details>

---

<details>
<summary><h2>Storage</h2></summary>

## Storage

Kubernetes provides several storage abstractions to manage persistent data for applications. Understanding storage concepts is essential for stateful applications.

### Storage Overview

Kubernetes storage system provides:
- **Persistent Volumes (PV)** - Cluster-wide storage resources
- **Persistent Volume Claims (PVC)** - User requests for storage
- **Storage Classes** - Dynamic provisioning of storage
- **Volume Types** - Various storage backends (local, cloud, network)

**Key Concepts:**
- **Static Provisioning:** Admin creates PVs manually
- **Dynamic Provisioning:** StorageClass automatically creates PVs
- **Binding:** PVC binds to available PV
- **Lifecycle:** PV and PVC have independent lifecycles

### Storage Architecture Diagram

```mermaid
graph TB
    Admin[Cluster Admin]
    User[User/Application]
    
    Admin -->|Creates| SC[StorageClass<br/>Defines Storage Type]
    Admin -->|Creates| PV[PersistentVolume<br/>Cluster Resource]
    
    User -->|Creates| PVC[PersistentVolumeClaim<br/>Storage Request]
    
    SC -->|Dynamically Provisions| PV2[PersistentVolume<br/>Auto-created]
    
    PVC -->|Binds to| PV
    PVC -->|Binds to| PV2
    
    Pod[Pod] -->|Uses| PVC
    PVC -->|Provides Storage| Pod
    
    style SC fill:#326ce5,color:#fff
    style PV fill:#4fc3f7,color:#000
    style PV2 fill:#4fc3f7,color:#000
    style PVC fill:#90caf9,color:#000
    style Pod fill:#f4a261,color:#000
```

---

### StorageClass

**StorageClass** provides a way to describe different classes of storage. It enables dynamic provisioning of PersistentVolumes.

#### What is a StorageClass?

A StorageClass:
- **Defines storage types** - Different classes of storage (fast, slow, SSD, HDD)
- **Enables dynamic provisioning** - Automatically creates PVs when PVCs are created
- **Provides parameters** - Storage-specific configuration
- **Abstracts storage backends** - Works with various storage systems

**Key Characteristics:**
- **Dynamic Provisioning:** Automatically creates PVs
- **Storage Abstraction:** Works with different storage backends
- **Parameters:** Storage-specific configuration
- **Default Class:** Can be marked as default

#### StorageClass Diagram

```mermaid
graph TB
    SC1[StorageClass: fast-ssd<br/>provisioner: kubernetes.io/aws-ebs]
    SC2[StorageClass: slow-hdd<br/>provisioner: kubernetes.io/aws-ebs]
    SC3[StorageClass: nfs<br/>provisioner: kubernetes.io/nfs]
    
    PVC1[PVC Request<br/>storageClassName: fast-ssd] -->|Triggers| SC1
    PVC2[PVC Request<br/>storageClassName: slow-hdd] -->|Triggers| SC2
    PVC3[PVC Request<br/>storageClassName: nfs] -->|Triggers| SC3
    
    SC1 -->|Creates| PV1[PV: fast-ssd-001]
    SC2 -->|Creates| PV2[PV: slow-hdd-001]
    SC3 -->|Creates| PV3[PV: nfs-001]
    
    PVC1 -->|Binds to| PV1
    PVC2 -->|Binds to| PV2
    PVC3 -->|Binds to| PV3
    
    style SC1 fill:#326ce5,color:#fff
    style SC2 fill:#326ce5,color:#fff
    style SC3 fill:#326ce5,color:#fff
    style PV1 fill:#4fc3f7,color:#000
    style PV2 fill:#4fc3f7,color:#000
    style PV3 fill:#4fc3f7,color:#000
    style PVC1 fill:#90caf9,color:#000
    style PVC2 fill:#90caf9,color:#000
    style PVC3 fill:#90caf9,color:#000
```

#### StorageClass Structure

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
provisioner: kubernetes.io/aws-ebs    # Provisioner plugin
parameters:
  type: gp3
  iops: "3000"
  encrypted: "true"
volumeBindingMode: Immediate         # Immediate or WaitForFirstConsumer
allowVolumeExpansion: true           # Allow volume expansion
reclaimPolicy: Delete                # Delete or Retain
```

#### StorageClass Fields Explained

**apiVersion:**
- API version for StorageClass
- Required: `storage.k8s.io/v1`

**kind:**
- Object type
- Required: `StorageClass`

**metadata:**
- Object metadata
- **name:** StorageClass name (required)
- **annotations:** Additional metadata
  - `storageclass.kubernetes.io/is-default-class: "true"` - Mark as default

**provisioner:**
- Provisioner plugin name (required)
- Examples:
  - `kubernetes.io/aws-ebs` - AWS EBS
  - `kubernetes.io/gce-pd` - Google Cloud Persistent Disk
  - `kubernetes.io/azure-disk` - Azure Disk
  - `kubernetes.io/cinder` - OpenStack Cinder
  - `kubernetes.io/nfs` - NFS

**parameters:**
- Storage-specific parameters
- Varies by provisioner
- Examples: type, iops, encrypted, zone

**volumeBindingMode:**
- **Immediate:** Bind immediately when PVC is created
- **WaitForFirstConsumer:** Wait until pod uses PVC

**allowVolumeExpansion:**
- Allow volume expansion (default: false)
- PVC can request more storage

**reclaimPolicy:**
- **Delete:** Delete PV when PVC is deleted (default for dynamic)
- **Retain:** Keep PV when PVC is deleted

#### Creating StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp3
  iops: "3000"
volumeBindingMode: Immediate
allowVolumeExpansion: true
reclaimPolicy: Delete
```

```bash
kubectl apply -f storageclass.yaml
```

#### Managing StorageClass

```bash
# List StorageClasses
kubectl get storageclass
kubectl get sc

# Get StorageClass details
kubectl get sc <storageclass-name>

# Describe StorageClass
kubectl describe sc <storageclass-name>

# Set default StorageClass
kubectl patch storageclass <name> -p '{"metadata":{"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'

# Delete StorageClass
kubectl delete sc <storageclass-name>
```

#### Common StorageClass Examples

**AWS EBS:**
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: aws-gp3
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp3
  iops: "3000"
  encrypted: "true"
```

**Google Cloud:**
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gce-ssd
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd
  replication-type: regional-pd
```

**Azure:**
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-premium
provisioner: kubernetes.io/azure-disk
parameters:
  storageaccounttype: Premium_LRS
```

#### Key Takeaways

1. **StorageClass enables dynamic provisioning** - Automatically creates PVs
2. **Defines storage types** - Different classes for different needs
3. **Provisioner-specific** - Each cloud provider has its own provisioner
4. **Can be default** - Mark one as default for automatic selection
5. **Volume binding modes** - Immediate or WaitForFirstConsumer

---

### PersistentVolume (PV)

**PersistentVolume (PV)** is a cluster-wide storage resource provisioned by an administrator or dynamically by a StorageClass.

#### What is a PersistentVolume?

A PersistentVolume:
- **Cluster resource** - Available to all namespaces
- **Storage abstraction** - Works with various storage backends
- **Lifecycle independent** - Exists independently of Pods
- **Bound to PVC** - Claimed by PersistentVolumeClaim

**Key Characteristics:**
- **Cluster-scoped** - Not namespaced
- **Static or Dynamic** - Created manually or automatically
- **Storage backend** - Can use various storage types
- **Reclaim policy** - What happens when released

#### PersistentVolume Diagram

```mermaid
graph TB
    Admin[Cluster Admin]
    
    Admin -->|Creates| PV1[PV: pv-001<br/>10Gi, RWO<br/>Available]
    Admin -->|Creates| PV2[PV: pv-002<br/>20Gi, RWO<br/>Available]
    Admin -->|Creates| PV3[PV: pv-003<br/>50Gi, RWX<br/>Available]
    
    PVC1[PVC: pvc-001<br/>Request: 10Gi, RWO] -->|Binds to| PV1
    PVC2[PVC: pvc-002<br/>Request: 20Gi, RWO] -->|Binds to| PV2
    
    PV1 -->|Bound| Status1[Status: Bound]
    PV2 -->|Bound| Status2[Status: Bound]
    PV3 -->|Available| Status3[Status: Available]
    
    Pod1[Pod 1] -->|Uses| PVC1
    Pod2[Pod 2] -->|Uses| PVC2
    
    style PV1 fill:#4fc3f7,color:#000
    style PV2 fill:#4fc3f7,color:#000
    style PV3 fill:#e3f2fd,color:#000
    style PVC1 fill:#90caf9,color:#000
    style PVC2 fill:#90caf9,color:#000
    style Status1 fill:#90ee90,color:#000
    style Status2 fill:#90ee90,color:#000
    style Status3 fill:#ffd700,color:#000
```

#### PersistentVolume Structure

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv
  labels:
    app: local
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  hostPath:
    path: /mnt/data
```

#### PersistentVolume Spec Fields Explained

**apiVersion:**
- API version for PersistentVolume
- Required: `v1`

**kind:**
- Object type
- Required: `PersistentVolume`

**metadata:**
- Object metadata
- **name:** PV name (required)
- **labels:** Key-value pairs for selection
- **annotations:** Additional metadata

**spec:**
- PersistentVolume specification
- **capacity:** Storage capacity
  - **storage:** Amount of storage (e.g., "10Gi", "100Mi")
- **accessModes:** How volume can be mounted
  - **ReadWriteOnce (RWO):** Single node read-write
  - **ReadOnlyMany (ROX):** Multiple nodes read-only
  - **ReadWriteMany (RWX):** Multiple nodes read-write
  - **ReadWriteOncePod (RWOP):** Single pod read-write
- **persistentVolumeReclaimPolicy:** What happens when released
  - **Retain:** Keep volume and data (manual cleanup)
  - **Recycle:** Delete data (deprecated)
  - **Delete:** Delete volume (for dynamic provisioning)
- **storageClassName:** StorageClass name (optional)
- **volumeMode:** Volume mode
  - **Filesystem:** Mount as filesystem (default)
  - **Block:** Use as raw block device
- **volume source:** Storage backend (one of):
  - **hostPath:** Local directory
  - **nfs:** NFS share
  - **awsElasticBlockStore:** AWS EBS
  - **gcePersistentDisk:** Google Cloud PD
  - **azureDisk:** Azure Disk
  - **cinder:** OpenStack Cinder
  - **local:** Local storage

#### PV States

**Available:**
- PV is available but not bound to any PVC

**Bound:**
- PV is bound to a PVC

**Released:**
- PVC is deleted but PV is not yet reclaimed

**Failed:**
- PV has failed its automatic reclamation

#### Creating PersistentVolume

**Method 1: Using YAML (Static Provisioning)**

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv
  labels:
    app: local
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  hostPath:
    path: /mnt/data
```

```bash
kubectl apply -f pv.yaml
```

**Method 2: Dynamic Provisioning**

PVs are automatically created by StorageClass when PVC is created.

#### Managing PersistentVolume

```bash
# List PersistentVolumes
kubectl get persistentvolumes
kubectl get pv

# Get PV details
kubectl get pv <pv-name>

# Describe PV
kubectl describe pv <pv-name>

# Delete PV
kubectl delete pv <pv-name>
```

#### Example Reference

For a practical example of a PersistentVolume YAML file, check out:

- **[nginx/persistentVolume.yml](./nginx/persistentVolume.yml)** - Example PersistentVolume definition

This example demonstrates:
- PV structure with capacity and access modes
- HostPath volume source (local storage)
- StorageClass assignment
- Reclaim policy configuration
- Basic PV pattern for local storage

**To use this example:**
```bash
# Apply the PersistentVolume
kubectl apply -f nginx/persistentVolume.yml

# View the PV
kubectl get pv -n nginx

# Check PV status
kubectl describe pv local-pv -n nginx

# View PV details
kubectl get pv local-pv -n nginx -o yaml
```

#### Key Takeaways

1. **PV is cluster-scoped** - Available to all namespaces
2. **Static or dynamic** - Created manually or by StorageClass
3. **Access modes** - Define how volume can be mounted
4. **Reclaim policy** - What happens when released
5. **Storage backend** - Various volume sources supported

---

### PersistentVolumeClaim (PVC)

**PersistentVolumeClaim (PVC)** is a request for storage by a user. It's like a "pod" for storage - it consumes PV resources.

#### What is a PersistentVolumeClaim?

A PersistentVolumeClaim:
- **Requests storage** - User requests storage with specific requirements
- **Binds to PV** - Automatically binds to matching PV
- **Namespaced** - Belongs to a namespace
- **Used by Pods** - Pods reference PVCs in volume mounts

**Key Characteristics:**
- **Namespaced resource** - Belongs to a namespace
- **Storage request** - Specifies size and access mode
- **Automatic binding** - Binds to matching PV
- **Dynamic provisioning** - Can trigger PV creation via StorageClass

#### PersistentVolumeClaim Diagram

```mermaid
graph TB
    User[User/Application]
    
    User -->|Creates| PVC1[PVC: pvc-001<br/>Request: 10Gi, RWO<br/>storageClassName: fast-ssd]
    
    SC[StorageClass: fast-ssd] -->|Dynamically Creates| PV1[PV: pv-001<br/>10Gi, RWO]
    
    PVC1 -->|Binds to| PV1
    
    Pod1[Pod 1] -->|References| PVC1
    Pod2[Pod 2] -->|References| PVC1
    
    PVC1 -->|Provides Storage| Pod1
    PVC1 -->|Provides Storage| Pod2
    
    style PVC1 fill:#90caf9,color:#000
    style PV1 fill:#4fc3f7,color:#000
    style SC fill:#326ce5,color:#fff
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
```

#### PersistentVolumeClaim Structure

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-pvc
  namespace: nginx
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: local-storage
```

#### PersistentVolumeClaim Spec Fields Explained

**apiVersion:**
- API version for PersistentVolumeClaim
- Required: `v1`

**kind:**
- Object type
- Required: `PersistentVolumeClaim`

**metadata:**
- Object metadata
- **name:** PVC name (required)
- **namespace:** Namespace (required)
- **labels:** Key-value pairs for organization
- **annotations:** Additional metadata

**spec:**
- PersistentVolumeClaim specification
- **accessModes:** How volume can be mounted (required)
  - **ReadWriteOnce (RWO):** Single node read-write
  - **ReadOnlyMany (ROX):** Multiple nodes read-only
  - **ReadWriteMany (RWX):** Multiple nodes read-write
  - **ReadWriteOncePod (RWOP):** Single pod read-write
- **resources:** Resource requests (required)
  - **requests:** Minimum resources required
    - **storage:** Amount of storage (e.g., "10Gi", "100Mi")
- **storageClassName:** StorageClass name (optional)
  - If specified, uses that StorageClass
  - If empty, uses default StorageClass
  - If `""`, uses static provisioning only
- **selector:** Label selector for PV binding (optional)
  - **matchLabels:** Match PV labels
  - **matchExpressions:** Match expressions
- **volumeName:** Specific PV name to bind to (optional)
- **volumeMode:** Volume mode (optional)
  - **Filesystem:** Mount as filesystem (default)
  - **Block:** Use as raw block device

#### PVC States

**Pending:**
- PVC is waiting for a PV to bind to

**Bound:**
- PVC is bound to a PV

**Lost:**
- PV that PVC was bound to no longer exists

#### Creating PersistentVolumeClaim

**Method 1: Using YAML**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-pvc
  namespace: nginx
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: local-storage
```

```bash
kubectl apply -f pvc.yaml
```

**Method 2: Using kubectl**

```bash
# Create PVC imperatively
kubectl create pvc my-pvc --storageclass=fast-ssd --size=10Gi --access-mode=ReadWriteOnce
```

#### Managing PersistentVolumeClaim

```bash
# List PVCs
kubectl get persistentvolumeclaims
kubectl get pvc

# List PVCs in namespace
kubectl get pvc -n <namespace>

# Get PVC details
kubectl get pvc <pvc-name>

# Describe PVC
kubectl describe pvc <pvc-name>

# Delete PVC
kubectl delete pvc <pvc-name>
```

#### Using PVC in Pods

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    volumeMounts:
    - name: data
      mountPath: /data
  volumes:
  - name: data
    persistentVolumeClaim:
      claimName: local-pvc
```

#### Example Reference

For a practical example of a PersistentVolumeClaim YAML file, check out:

- **[nginx/persistentVolumeClaim.yml](./nginx/persistentVolumeClaim.yml)** - Example PersistentVolumeClaim definition

This example demonstrates:
- PVC structure with access modes
- Storage resource request
- StorageClass assignment
- Basic PVC pattern for storage request

**To use this example:**
```bash
# Apply the PersistentVolumeClaim
kubectl apply -f nginx/persistentVolumeClaim.yml

# View the PVC
kubectl get pvc -n nginx

# Check PVC status
kubectl describe pvc local-pvc -n nginx

# View PVC details
kubectl get pvc local-pvc -n nginx -o yaml

# Check if PVC is bound
kubectl get pvc local-pvc -n nginx
```

#### Key Takeaways

1. **PVC is namespaced** - Belongs to a namespace
2. **Requests storage** - Specifies size and access mode
3. **Binds to PV** - Automatically binds to matching PV
4. **Dynamic provisioning** - Can trigger PV creation via StorageClass
5. **Used by Pods** - Pods reference PVCs in volume mounts

---

### Storage Workflow

**Static Provisioning:**
1. Admin creates PV
2. User creates PVC
3. PVC binds to PV
4. Pod uses PVC

**Dynamic Provisioning:**
1. Admin creates StorageClass
2. User creates PVC (with storageClassName)
3. StorageClass creates PV automatically
4. PVC binds to PV
5. Pod uses PVC

### Storage Best Practices

1. **Use StorageClass** - Prefer dynamic provisioning
2. **Set appropriate access modes** - Based on use case
3. **Set reclaim policy** - Retain for important data
4. **Use labels** - For PV selection
5. **Monitor storage** - Check PV/PVC status
6. **Clean up** - Delete unused PVCs and PVs

</details>

---

<details>
<summary><h2>Services</h2></summary>

## Services

**Services** provide a stable network endpoint to access Pods. They abstract away the dynamic nature of Pods and provide a consistent way to communicate with applications running in Kubernetes.

### What is a Service?

Think of a Service as a **stable front door** to your application. Here's why we need Services:

**The Problem:**
- Pods are **ephemeral** - They can be created, destroyed, or moved
- Pods get **new IP addresses** each time they're created
- How do you connect to an application when its IP keeps changing?

**The Solution:**
- Service provides a **stable IP address** and DNS name
- Service **load balances** traffic to multiple Pods
- Service **abstracts** the underlying Pods
- Other applications connect to the Service, not directly to Pods

**Simple Analogy:**
Imagine a restaurant:
- **Pods** = Individual waiters (they come and go)
- **Service** = The host/hostess desk (always there, directs you to available waiters)
- You don't need to know which waiter will serve you - you just go to the desk!

### Why Do We Need Services?

**1. Pod IPs are Dynamic**
```
Pod 1: 10.244.1.5  → Gets deleted
Pod 2: 10.244.1.6  → Gets new IP: 10.244.1.10
Pod 3: 10.244.1.7  → Gets new IP: 10.244.1.11
```
Without Service: Application breaks when Pod IPs change
With Service: Service IP stays the same, routes to new Pod IPs

**2. Load Balancing**
- Multiple Pods running the same application
- Service distributes traffic across all Pods
- Automatic load balancing

**3. Service Discovery**
- Services get DNS names
- Other Pods can find services by name
- No need to hardcode IP addresses

### Service Diagram

```mermaid
graph TB
    Client[Client Application]
    
    Client -->|Connects to| SVC[Service<br/>nginx-service<br/>10.96.0.1:80<br/>DNS: nginx-service.default.svc]
    
    SVC -->|Load Balances| Pod1[Pod 1<br/>10.244.1.5:80<br/>app=nginx]
    SVC -->|Load Balances| Pod2[Pod 2<br/>10.244.1.6:80<br/>app=nginx]
    SVC -->|Load Balances| Pod3[Pod 3<br/>10.244.1.7:80<br/>app=nginx]
    
    Pod1 -.->|Dies| SVC
    SVC -->|Routes to| Pod4[Pod 4<br/>10.244.1.10:80<br/>app=nginx<br/>Replacement]
    
    style SVC fill:#326ce5,color:#fff
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
    style Pod3 fill:#f4a261,color:#000
    style Pod4 fill:#90ee90,color:#000
    style Client fill:#e3f2fd,color:#000
```

### Service Types

Kubernetes provides different Service types for different use cases:

**1. ClusterIP (Default)**
- **Internal only** - Accessible only within the cluster
- **Stable IP** - Gets a cluster-internal IP
- **Use case:** Communication between Pods in the cluster

**2. NodePort**
- **External access** - Accessible from outside the cluster
- **Opens port** - Opens a port on each node
- **Use case:** Development, testing, simple external access

**3. LoadBalancer**
- **Cloud provider** - Creates external load balancer
- **Public IP** - Gets a public IP address
- **Use case:** Production applications with cloud providers

**4. ExternalName**
- **External service** - Maps to external DNS name
- **No proxy** - Returns CNAME record
- **Use case:** Accessing external services

### Service Structure

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: nginx
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

### Service Spec Fields Explained

**apiVersion:**
- API version for Service
- Required: `v1`

**kind:**
- Object type
- Required: `Service`

**metadata:**
- Object metadata
- **name:** Service name (required) - Used for DNS
- **namespace:** Namespace (optional, defaults to default)
- **labels:** Key-value pairs for organization
- **annotations:** Additional metadata

**spec:**
- Service specification
- **selector:** Label selector to find Pods (required)
  - **matchLabels:** Key-value pairs matching Pod labels
  - Service routes traffic to Pods with matching labels
- **ports:** Port configuration (required)
  - **port:** Port exposed by Service
  - **targetPort:** Port on Pods (defaults to port if not specified)
  - **protocol:** TCP (default), UDP, or SCTP
  - **name:** Port name (optional, for named ports)
- **type:** Service type (optional, default: ClusterIP)
  - **ClusterIP:** Internal cluster IP
  - **NodePort:** Exposes on node IPs
  - **LoadBalancer:** Creates external load balancer
  - **ExternalName:** Maps to external DNS
- **clusterIP:** Specific cluster IP (optional, auto-assigned if not specified)
- **sessionAffinity:** Session affinity (optional)
  - **None:** No session affinity (default)
  - **ClientIP:** Route same client to same Pod

### Service Types Explained

#### 1. ClusterIP Service

**What it is:**
- Default Service type
- Internal cluster IP address
- Accessible only within the cluster

**Use Case:**
- Communication between Pods
- Internal services
- Database connections

**Example:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP  # Default, can be omitted
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

**Access:**
- From within cluster: `http://nginx-service:80`
- DNS: `nginx-service.default.svc.cluster.local`

#### 2. NodePort Service

**What it is:**
- Opens a port on each node
- Accessible from outside cluster via `<NodeIP>:<NodePort>`
- Also accessible via ClusterIP

**Use Case:**
- Development and testing
- Simple external access
- When LoadBalancer is not available

**Example:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080  # Optional, auto-assigned if not specified (30000-32767)
```

**Access:**
- From outside: `http://<NodeIP>:30080`
- From inside: `http://nginx-service:80`

#### 3. LoadBalancer Service

**What it is:**
- Creates external load balancer (cloud provider)
- Gets a public IP address
- Automatically includes NodePort and ClusterIP

**Use Case:**
- Production applications
- Public-facing services
- Cloud environments (AWS, GCP, Azure)

**Example:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

**Access:**
- Public IP provided by cloud provider
- Automatically routes to Pods

#### 4. ExternalName Service

**What it is:**
- Maps Service to external DNS name
- Returns CNAME record
- No proxy, no load balancing

**Use Case:**
- Accessing external services
- Migrating to Kubernetes
- Service abstraction

**Example:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-db
spec:
  type: ExternalName
  externalName: database.example.com
```

**Access:**
- DNS resolves to external name
- No proxy involved

### Service Discovery

**DNS Names:**
- Services get DNS names automatically
- Format: `<service-name>.<namespace>.svc.cluster.local`
- Short form: `<service-name>` (same namespace)
- Short form: `<service-name>.<namespace>` (different namespace)

**Examples:**
```bash
# Same namespace
http://nginx-service:80

# Different namespace
http://nginx-service.nginx:80

# Full DNS name
http://nginx-service.nginx.svc.cluster.local:80
```

### Creating Services

**Method 1: Using YAML (Recommended)**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: nginx
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

```bash
kubectl apply -f service.yaml
```

**Method 2: Using kubectl**

```bash
# Create Service from Deployment
kubectl expose deployment nginx-deployment --port=80 --target-port=80

# Create Service with specific type
kubectl expose deployment nginx-deployment --type=NodePort --port=80

# Create Service with LoadBalancer
kubectl expose deployment nginx-deployment --type=LoadBalancer --port=80
```

### Managing Services

```bash
# List Services
kubectl get services
kubectl get svc

# List Services in namespace
kubectl get svc -n <namespace>

# Get Service details
kubectl get svc <service-name>

# Describe Service
kubectl describe svc <service-name>

# Get Service endpoints
kubectl get endpoints <service-name>

# Delete Service
kubectl delete svc <service-name>
```

### Service Endpoints

**Endpoints:**
- Service automatically creates Endpoints object
- Contains list of Pod IPs matching selector
- Updated automatically when Pods change

```bash
# View endpoints
kubectl get endpoints <service-name>

# Output shows:
# NAME            ENDPOINTS
# nginx-service   10.244.1.5:80,10.244.1.6:80,10.244.1.7:80
```

### Service Load Balancing

**How it Works:**
1. Service receives request
2. kube-proxy routes to one of the Pods
3. Uses round-robin by default
4. Can use session affinity for sticky sessions

**Load Balancing Methods:**
- **Round-robin (default):** Distributes evenly
- **Session affinity:** Same client to same Pod

### Service Best Practices

1. **Use meaningful names** - Service names become DNS names
2. **Match selector labels** - Ensure selector matches Pod labels
3. **Use ClusterIP for internal** - Default for internal communication
4. **Use LoadBalancer for external** - For public-facing services
5. **Set targetPort correctly** - Match container port
6. **Use named ports** - For flexibility
7. **Monitor endpoints** - Check if Pods are healthy

### Common Service Patterns

**Pattern 1: Basic Service**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 8080
```

**Pattern 2: Multiple Ports**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: myapp
  ports:
    - name: http
      port: 80
      targetPort: 8080
    - name: https
      port: 443
      targetPort: 8443
```

**Pattern 3: Headless Service**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: headless-service
spec:
  clusterIP: None  # Headless service
  selector:
    app: stateful
  ports:
    - port: 80
```

### Example Reference

For a practical example of a Service YAML file, check out:

- **[nginx/service.yml](./nginx/service.yml)** - Example Service definition

This example demonstrates:
- Service structure with selector
- Port configuration (port and targetPort)
- ClusterIP service type (default)
- Basic Service pattern for nginx

**To use this example:**
```bash
# Apply the Service
kubectl apply -f nginx/service.yml

# View the Service
kubectl get svc -n nginx

# Check Service details
kubectl describe svc nginx-service -n nginx

# View Service endpoints (Pods it routes to)
kubectl get endpoints nginx-service -n nginx

# Test Service from within cluster
kubectl run test-pod --image=busybox -it --rm --restart=Never -n nginx -- wget -qO- http://nginx-service:80

# Get Service cluster IP
kubectl get svc nginx-service -n nginx -o jsonpath='{.spec.clusterIP}'
```

### Key Takeaways

1. **Services provide stable endpoints** - Stable IP and DNS name
2. **Load balancing** - Distributes traffic across Pods
3. **Service discovery** - DNS names for easy access
4. **Different types** - ClusterIP, NodePort, LoadBalancer, ExternalName
5. **Selector-based** - Routes to Pods matching labels
6. **Automatic updates** - Endpoints update when Pods change
7. **Internal by default** - ClusterIP for internal communication
8. **Essential for Pod communication** - Pods communicate via Services

Services are essential for networking in Kubernetes. They provide the stable, reliable way to access your applications regardless of Pod changes.

</details>

---

<details>
<summary><h2>Ingress</h2></summary>

## Ingress

**Ingress** is an API object that manages external HTTP and HTTPS access to Services in a cluster. It provides HTTP/HTTPS routing, SSL/TLS termination, and name-based virtual hosting.

### What is Ingress?

**Simple Explanation:**
Ingress is like a **smart traffic director** at the entrance of your cluster. It:
- **Routes traffic** based on URL paths and hostnames
- **Terminates SSL/TLS** - Handles HTTPS certificates
- **Provides a single entry point** - One IP for multiple services
- **Works with Services** - Routes to ClusterIP Services

**Analogy:**
Think of Ingress as a **receptionist** in a building:
- **Services** = Individual offices (ClusterIP services are internal)
- **Ingress** = Receptionist who directs visitors to the right office based on who they're looking for
- **Ingress Controller** = The actual person/system doing the directing

### Why Do We Need Ingress?

**The Problem with Services:**
- **LoadBalancer:** Creates one load balancer per service (expensive)
- **NodePort:** Requires managing ports and firewall rules
- **No path-based routing:** Can't route based on URL paths
- **No SSL termination:** Each service needs to handle SSL
- **No name-based routing:** Can't route based on domain names

**The Solution - Ingress:**
- **Single entry point** - One load balancer for multiple services
- **Path-based routing** - Route `/api` to one service, `/web` to another
- **Host-based routing** - Route `api.example.com` to one service, `web.example.com` to another
- **SSL/TLS termination** - Handle certificates at Ingress level
- **Cost-effective** - One load balancer instead of many

### Ingress vs Services

| Feature | Service | Ingress |
|---------|---------|---------|
| **Layer** | L4 (TCP/UDP) | L7 (HTTP/HTTPS) |
| **Routing** | IP/Port based | Path/Host based |
| **SSL/TLS** | No termination | SSL/TLS termination |
| **External Access** | NodePort/LoadBalancer | HTTP/HTTPS routing |
| **Cost** | One LB per service | One LB for all |
| **Use Case** | Internal/external access | HTTP/HTTPS routing |

### Ingress Architecture Diagram

```mermaid
graph TB
    Internet[Internet]
    
    Internet -->|HTTP/HTTPS| LB[Load Balancer<br/>External IP]
    
    LB -->|Routes Traffic| IC[Ingress Controller<br/>Pod in Cluster]
    
    IC -->|Reads Rules| Ingress[Ingress Resource<br/>Routing Rules]
    
    Ingress -->|Path: /api| SVC1[Service: api-service<br/>ClusterIP]
    Ingress -->|Path: /web| SVC2[Service: web-service<br/>ClusterIP]
    Ingress -->|Host: api.example.com| SVC1
    Ingress -->|Host: web.example.com| SVC2
    
    SVC1 -->|Routes to| Pod1[API Pods]
    SVC2 -->|Routes to| Pod2[Web Pods]
    
    style LB fill:#326ce5,color:#fff
    style IC fill:#4fc3f7,color:#000
    style Ingress fill:#90caf9,color:#000
    style SVC1 fill:#e3f2fd,color:#000
    style SVC2 fill:#e3f2fd,color:#000
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
```

### Ingress Controller

**What is an Ingress Controller?**

An Ingress Controller is the **actual implementation** that processes Ingress rules. It's a Pod that:
- **Watches Ingress resources** - Monitors for Ingress changes
- **Implements routing rules** - Processes routing logic
- **Handles SSL/TLS** - Terminates SSL connections
- **Manages load balancer** - Configures external load balancer

**Popular Ingress Controllers:**
- **NGINX Ingress Controller** - Most popular
- **Traefik** - Modern, feature-rich
- **HAProxy** - High performance
- **Istio Gateway** - Service mesh integration
- **AWS ALB Ingress Controller** - AWS-specific
- **GKE Ingress** - Google Cloud

**Important:**
- Ingress Controller must be **installed separately**
- Kubernetes doesn't provide a default Ingress Controller
- You need to deploy one before using Ingress

### Ingress Structure

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
  - host: web.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
  tls:
  - hosts:
    - api.example.com
    - web.example.com
    secretName: tls-secret
```

### Ingress Spec Fields Explained

**apiVersion:**
- API version for Ingress
- Required: `networking.k8s.io/v1` (v1.19+)
- Older: `networking.k8s.io/v1beta1` (deprecated)

**kind:**
- Object type
- Required: `Ingress`

**metadata:**
- Object metadata
- **name:** Ingress name (required)
- **namespace:** Namespace (optional, defaults to default)
- **labels:** Key-value pairs for organization
- **annotations:** Ingress Controller-specific settings
  - Controller-specific annotations for configuration

**spec:**
- Ingress specification
- **ingressClassName:** Ingress class name (v1.19+)
  - Specifies which Ingress Controller to use
  - Alternative to `kubernetes.io/ingress.class` annotation
- **rules:** List of routing rules (required)
  - **host:** Hostname (optional, matches all if not specified)
  - **http:** HTTP rules
    - **paths:** List of path rules
      - **path:** URL path (e.g., `/api`, `/web`)
      - **pathType:** Path matching type
        - **Exact:** Exact match
        - **Prefix:** Prefix match (most common)
        - **ImplementationSpecific:** Controller-specific
      - **backend:** Backend service
        - **service:** Service backend
          - **name:** Service name
          - **port:** Service port
            - **number:** Port number
            - **name:** Named port
- **tls:** TLS configuration (optional)
  - **hosts:** List of hostnames
  - **secretName:** Secret containing TLS certificate

### Ingress Routing Types

#### 1. Host-Based Routing

Routes traffic based on the **hostname** (domain name).

**Example:**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: host-based-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
  - host: web.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

**How it works:**
- `api.example.com` → routes to `api-service`
- `web.example.com` → routes to `web-service`

#### 2. Path-Based Routing

Routes traffic based on the **URL path**.

**Example:**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: path-based-ingress
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
      - path: /
        pathType: Prefix
        backend:
          service:
            name: default-service
            port:
              number: 80
```

**How it works:**
- `/api/*` → routes to `api-service`
- `/web/*` → routes to `web-service`
- `/*` → routes to `default-service`

**Important:** Order matters! More specific paths should come first.

#### 3. Combined Host and Path Routing

Combines both hostname and path for routing.

**Example:**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: combined-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

### Path Types Explained

**1. Exact:**
- Exact match of the path
- `/api` matches only `/api`, not `/api/v1`

**2. Prefix:**
- Matches path prefix
- `/api` matches `/api`, `/api/v1`, `/api/users`
- Most commonly used

**3. ImplementationSpecific:**
- Controller-specific matching
- Behavior depends on Ingress Controller

### Ingress Routing Diagram

```mermaid
graph TB
    User[User Request]
    
    User -->|api.example.com/api/users| Ingress[Ingress Controller]
    
    Ingress -->|Host: api.example.com| Rule1[Rule 1: api.example.com]
    Ingress -->|Host: web.example.com| Rule2[Rule 2: web.example.com]
    Ingress -->|Path: /api| Rule3[Rule 3: Path /api]
    Ingress -->|Path: /web| Rule4[Rule 4: Path /web]
    
    Rule1 -->|Routes to| SVC1[api-service:80]
    Rule2 -->|Routes to| SVC2[web-service:80]
    Rule3 -->|Routes to| SVC1
    Rule4 -->|Routes to| SVC2
    
    SVC1 --> Pod1[API Pods]
    SVC2 --> Pod2[Web Pods]
    
    style Ingress fill:#326ce5,color:#fff
    style Rule1 fill:#90caf9,color:#000
    style Rule2 fill:#90caf9,color:#000
    style Rule3 fill:#90caf9,color:#000
    style Rule4 fill:#90caf9,color:#000
    style SVC1 fill:#e3f2fd,color:#000
    style SVC2 fill:#e3f2fd,color:#000
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
```

### TLS/SSL Termination

Ingress can handle SSL/TLS termination, so your Services don't need to.

**How it works:**
1. Client connects via HTTPS
2. Ingress Controller terminates SSL
3. Forwards HTTP to backend Service
4. Service receives plain HTTP

**Example:**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-ingress
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - api.example.com
    - web.example.com
    secretName: tls-secret
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
```

**Creating TLS Secret:**
```bash
# Create TLS secret
kubectl create secret tls tls-secret \
  --cert=tls.crt \
  --key=tls.key
```

### Ingress Annotations

Ingress Controllers use annotations for additional configuration.

**NGINX Ingress Controller Annotations:**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: annotated-ingress
  annotations:
    # Rewrite target
    nginx.ingress.kubernetes.io/rewrite-target: /
    
    # SSL redirect
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    
    # Rate limiting
    nginx.ingress.kubernetes.io/limit-rps: "100"
    
    # CORS
    nginx.ingress.kubernetes.io/enable-cors: "true"
    
    # Authentication
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: basic-auth
    
    # Custom headers
    nginx.ingress.kubernetes.io/configuration-snippet: |
      more_set_headers "X-Custom-Header: value";
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

### Ingress Class

**What is Ingress Class?**

Ingress Class allows you to have multiple Ingress Controllers in the same cluster.

**IngressClass Resource:**
```yaml
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: nginx
spec:
  controller: k8s.io/ingress-nginx
```

**Using IngressClass:**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  ingressClassName: nginx  # References IngressClass
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

### Creating Ingress

**Method 1: Using YAML**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
```

```bash
kubectl apply -f ingress.yaml
```

**Method 2: Using kubectl**

```bash
# Create Ingress from Service
kubectl create ingress example-ingress \
  --class=nginx \
  --rule="api.example.com/*=api-service:80" \
  --rule="web.example.com/*=web-service:80"
```

### Managing Ingress

```bash
# List Ingresses
kubectl get ingress
kubectl get ing

# List Ingresses in namespace
kubectl get ing -n <namespace>

# Get Ingress details
kubectl get ing <ingress-name>

# Describe Ingress
kubectl describe ing <ingress-name>

# Get Ingress YAML
kubectl get ing <ingress-name> -o yaml

# Delete Ingress
kubectl delete ing <ingress-name>
```

### Installing Ingress Controller

**NGINX Ingress Controller (Example):**

```bash
# Using kubectl
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/cloud/deploy.yaml

# Using Helm
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install ingress-nginx ingress-nginx/ingress-nginx

# Check installation
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

### Ingress Controller Architecture

```mermaid
graph TB
    LB[Load Balancer<br/>External IP]
    
    LB -->|Routes to| IC[Ingress Controller<br/>Pod with Service<br/>Type: LoadBalancer]
    
    IC -->|Watches| Ingress1[Ingress Resource 1]
    IC -->|Watches| Ingress2[Ingress Resource 2]
    
    IC -->|Reads Rules| Rules[Routing Rules]
    
    Rules -->|Routes| SVC1[Service 1]
    Rules -->|Routes| SVC2[Service 2]
    Rules -->|Routes| SVC3[Service 3]
    
    SVC1 --> Pod1[Pods]
    SVC2 --> Pod2[Pods]
    SVC3 --> Pod3[Pods]
    
    style LB fill:#326ce5,color:#fff
    style IC fill:#4fc3f7,color:#000
    style Ingress1 fill:#90caf9,color:#000
    style Ingress2 fill:#90caf9,color:#000
    style SVC1 fill:#e3f2fd,color:#000
    style SVC2 fill:#e3f2fd,color:#000
    style SVC3 fill:#e3f2fd,color:#000
```

### Complete Ingress Example

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: complete-ingress
  namespace: production
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/rate-limit: "100"
spec:
  ingressClassName: nginx
  rules:
  # Host-based routing
  - host: api.example.com
    http:
      paths:
      - path: /v1
        pathType: Prefix
        backend:
          service:
            name: api-v1-service
            port:
              number: 80
      - path: /v2
        pathType: Prefix
        backend:
          service:
            name: api-v2-service
            port:
              number: 80
  # Path-based routing
  - http:
      paths:
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
      - path: /admin
        pathType: Prefix
        backend:
          service:
            name: admin-service
            port:
              number: 80
  # TLS configuration
  tls:
  - hosts:
    - api.example.com
    secretName: api-tls-secret
```

### Ingress Best Practices

1. **Install Ingress Controller first** - Required before using Ingress
2. **Use ingressClassName** - Specify which controller to use
3. **Order paths correctly** - More specific paths first
4. **Use pathType: Prefix** - Most common and flexible
5. **Handle TLS at Ingress** - Don't handle SSL in Services
6. **Use annotations wisely** - Controller-specific features
7. **Monitor Ingress Controller** - Check controller health
8. **Use meaningful names** - For better organization
9. **Test routing rules** - Verify paths work correctly
10. **Secure with TLS** - Use TLS for production

### Common Ingress Patterns

**Pattern 1: Simple Path Routing**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: simple-ingress
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

**Pattern 2: Multiple Services**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: multi-service-ingress
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

**Pattern 3: Host-Based with TLS**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-host-ingress
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - api.example.com
    secretName: api-tls-secret
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
```

### Troubleshooting Ingress

**Common Issues:**

1. **Ingress not working:**
   ```bash
   # Check Ingress Controller
   kubectl get pods -n ingress-nginx
   
   # Check Ingress status
   kubectl describe ing <ingress-name>
   
   # Check Service
   kubectl get svc <service-name>
   ```

2. **404 errors:**
   - Check path matching
   - Verify pathType
   - Check Service name and port

3. **SSL errors:**
   - Verify TLS secret exists
   - Check certificate validity
   - Verify secret name matches

4. **Routing not working:**
   - Check Ingress Controller logs
   - Verify rules are correct
   - Check Service endpoints

### Example Reference

For a practical example of an Ingress YAML file, check out:

- **[nginx/ingress.yml](./nginx/ingress.yml)** - Example Ingress definition

This example demonstrates:
- Ingress structure with multiple path-based routing rules
- Path-based routing for different services (`/nginx` and `/`)
- NGINX Ingress Controller annotations (rewrite-target)
- Multiple backend services (nginx-service and notes-app-service)
- Different port configurations (80 and 8000)
- Path ordering (more specific path `/nginx` before default path `/`)

**To use this example:**
```bash
# Apply the Ingress
kubectl apply -f nginx/ingress.yml

# View the Ingress
kubectl get ingress -n nginx

# Check Ingress details
kubectl describe ingress nginx-notes-ingress -n nginx

# View Ingress YAML
kubectl get ingress nginx-notes-ingress -n nginx -o yaml

# Check Ingress status
kubectl get ingress nginx-notes-ingress -n nginx

# Test routing (if Ingress Controller is installed)
# /nginx path should route to nginx-service
# / path should route to notes-app-service
```

**Important Notes:**
- Ensure Ingress Controller is installed before applying
- Verify that `nginx-service` and `notes-app-service` exist in the nginx namespace
- The rewrite-target annotation rewrites the URL path
- Path order matters - `/nginx` must come before `/` to work correctly

### Key Takeaways

1. **Ingress provides HTTP/HTTPS routing** - Layer 7 routing
2. **Requires Ingress Controller** - Must be installed separately
3. **Path and host-based routing** - Flexible routing options
4. **SSL/TLS termination** - Handle certificates at Ingress
5. **Single entry point** - One load balancer for multiple services
6. **Works with Services** - Routes to ClusterIP Services
7. **Controller-specific** - Annotations vary by controller
8. **Cost-effective** - One LB instead of many
9. **Use ingressClassName** - Specify controller (v1.19+)
10. **Order matters** - More specific paths first

Ingress is essential for managing external HTTP/HTTPS access to your Kubernetes services. It provides a powerful, flexible way to route traffic based on paths and hostnames while handling SSL/TLS termination.

</details>

---

<details>
<summary><h2>ConfigMap and Secrets</h2></summary>

## ConfigMap and Secrets

**ConfigMap** and **Secrets** are Kubernetes objects used to store configuration data and sensitive information separately from application code. They allow you to decouple configuration from container images.

### ConfigMap

**ConfigMap** is an API object used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, configuration files, or command-line arguments.

#### What is a ConfigMap?

**Simple Explanation:**
ConfigMap is like a **configuration file** that lives outside your application code. It stores:
- **Configuration data** - Settings, parameters, URLs
- **Non-sensitive data** - Database names, feature flags, API endpoints
- **Environment-specific values** - Different values for dev, staging, prod

**Why Use ConfigMap?**
- **Separate config from code** - Change config without rebuilding images
- **Environment-specific** - Same image, different configs
- **Reusable** - Share config across multiple Pods
- **Version control** - Track config changes

#### ConfigMap Diagram

```mermaid
graph TB
    CM[ConfigMap<br/>app-config<br/>Key-Value Pairs]
    
    CM -->|Provides| Pod1[Pod 1<br/>Uses ConfigMap]
    CM -->|Provides| Pod2[Pod 2<br/>Uses ConfigMap]
    CM -->|Provides| Pod3[Pod 3<br/>Uses ConfigMap]
    
    Pod1 -->|Environment Variables| Env1[ENV_VAR equals value]
    Pod1 -->|Config Files| File1[Config File Mount]
    
    Pod2 -->|Environment Variables| Env2[ENV_VAR equals value]
    Pod3 -->|Command Args| Args[Command Arguments]
    
    style CM fill:#326ce5,color:#fff
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
    style Pod3 fill:#f4a261,color:#000
```

#### ConfigMap Structure

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: default
data:
  # Simple key-value pairs
  database_url: "mysql://db.example.com:3306"
  api_endpoint: "https://api.example.com"
  log_level: "info"
  
  # Multi-line values
  config.yaml: |
    server:
      port: 8080
      host: 0.0.0.0
    database:
      name: myapp
      pool_size: 10
```

#### ConfigMap Spec Fields Explained

**apiVersion:**
- API version for ConfigMap
- Required: `v1`

**kind:**
- Object type
- Required: `ConfigMap`

**metadata:**
- Object metadata
- **name:** ConfigMap name (required)
- **namespace:** Namespace (optional, defaults to default)
- **labels:** Key-value pairs for organization
- **annotations:** Additional metadata

**data:**
- Configuration data (key-value pairs)
- **Keys:** String keys
- **Values:** String values (can be multi-line)
- All values are treated as strings

**binaryData:**
- Binary data (optional)
- Base64-encoded binary data
- Use for binary files (images, certificates)

#### Creating ConfigMap

**Method 1: Using YAML**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-config-map
  namespace: mysql
data:
  MYSQL_DATABASE: devops
```

```bash
kubectl apply -f configmap.yaml
```

**Method 2: Using kubectl**

```bash
# Create from literal values
kubectl create configmap app-config \
  --from-literal=database_url=mysql://localhost:3306 \
  --from-literal=log_level=info

# Create from file
kubectl create configmap app-config \
  --from-file=config.properties

# Create from directory
kubectl create configmap app-config \
  --from-file=/path/to/config/dir

# Create from env file
kubectl create configmap app-config \
  --from-env-file=config.env
```

#### Using ConfigMap in Pods

**Method 1: Environment Variables**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    env:
    - name: DATABASE_URL
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database_url
    - name: LOG_LEVEL
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: log_level
```

**Method 2: All Keys as Environment Variables**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    envFrom:
    - configMapRef:
        name: app-config
```

**Method 3: Volume Mount (Config File)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: app-config
```

**Method 4: Specific Keys as Files**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: app-config
      items:
      - key: config.yaml
        path: app.yaml
```

#### Managing ConfigMap

```bash
# List ConfigMaps
kubectl get configmaps
kubectl get cm

# List ConfigMaps in namespace
kubectl get cm -n <namespace>

# Get ConfigMap details
kubectl get cm <configmap-name>

# Describe ConfigMap
kubectl describe cm <configmap-name>

# View ConfigMap data
kubectl get cm <configmap-name> -o yaml

# Edit ConfigMap
kubectl edit cm <configmap-name>

# Delete ConfigMap
kubectl delete cm <configmap-name>
```

#### ConfigMap Best Practices

1. **Store non-sensitive data only** - Use Secrets for sensitive data
2. **Use meaningful names** - Clear, descriptive names
3. **Keep values small** - ConfigMaps have size limits (1MB)
4. **Use namespaces** - Organize by namespace
5. **Version control** - Store ConfigMaps in Git
6. **Immutable ConfigMaps** - Set `immutable: true` for stability
7. **Use labels** - For organization and selection

#### Example Reference

For a practical example of a ConfigMap YAML file, check out:

- **[mysql/configmap.yml](./mysql/configmap.yml)** - Example ConfigMap definition

This example demonstrates:
- ConfigMap structure with data field
- Simple key-value configuration
- Database configuration pattern
- Namespace assignment

**To use this example:**
```bash
# Apply the ConfigMap
kubectl apply -f mysql/configmap.yml

# View the ConfigMap
kubectl get cm -n mysql

# Check ConfigMap details
kubectl describe cm mysql-config-map -n mysql

# View ConfigMap data
kubectl get cm mysql-config-map -n mysql -o yaml

# Use in Pod (example)
# Reference this ConfigMap in Pod spec using configMapKeyRef or envFrom
```

#### Key Takeaways

1. **ConfigMap stores non-sensitive data** - Configuration, not secrets
2. **Key-value pairs** - Simple string key-value storage
3. **Multiple usage methods** - Env vars, files, command args
4. **Namespace-scoped** - Belongs to a namespace
5. **Separate config from code** - Change without rebuilding
6. **Reusable** - Share across multiple Pods
7. **Size limit** - 1MB per ConfigMap

---

### Secrets

**Secret** is an API object used to store sensitive data, such as passwords, OAuth tokens, and SSH keys. Secrets are base64-encoded but not encrypted by default.

#### What is a Secret?

**Simple Explanation:**
Secret is like a **secure vault** for sensitive information. It stores:
- **Passwords** - Database passwords, API keys
- **Tokens** - OAuth tokens, API tokens
- **Certificates** - TLS certificates, SSH keys
- **Sensitive config** - Any data that shouldn't be visible

**Why Use Secrets?**
- **Security** - Separate sensitive data from code
- **Base64 encoding** - At least some obfuscation
- **Access control** - RBAC can control access
- **Encryption at rest** - Can be encrypted in etcd

**Important:**
- Secrets are **base64-encoded**, not encrypted
- Anyone with access can decode them
- Use RBAC to restrict access
- Consider external secret management for production

#### Secret Diagram

```mermaid
graph TB
    Secret[Secret<br/>app-secret<br/>Base64 Encoded]
    
    Secret -->|Provides| Pod1[Pod 1<br/>Uses Secret]
    Secret -->|Provides| Pod2[Pod 2<br/>Uses Secret]
    
    Pod1 -->|Environment Variables| Env1[PASSWORD masked]
    Pod1 -->|Volume Mount| File1[Secret File Mount]
    
    Pod2 -->|Environment Variables| Env2[API_KEY masked]
    
    Secret -.->|RBAC Controls| Access[Access Control]
    
    style Secret fill:#d32f2f,color:#fff
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#f4a261,color:#000
    style Access fill:#ff9800,color:#fff
```

#### Secret Structure

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
  namespace: default
type: Opaque
data:
  # Base64-encoded values
  password: cGFzc3dvcmQxMjM=  # "password123"
  api_key: YWJjZGVmZ2hpams=    # "abcdefghij"
stringData:
  # Plain text (automatically encoded)
  username: admin
  token: my-secret-token
```

#### Secret Types

**1. Opaque (Default)**
- Arbitrary user-defined data
- Most common type

**2. kubernetes.io/dockerconfigjson**
- Docker registry credentials
- For pulling private images

**3. kubernetes.io/tls**
- TLS certificate and key
- For TLS/SSL

**4. kubernetes.io/service-account-token**
- Service account token
- Created automatically

#### Secret Spec Fields Explained

**apiVersion:**
- API version for Secret
- Required: `v1`

**kind:**
- Object type
- Required: `Secret`

**metadata:**
- Object metadata
- **name:** Secret name (required)
- **namespace:** Namespace (optional, defaults to default)
- **labels:** Key-value pairs for organization
- **annotations:** Additional metadata

**type:**
- Secret type (optional, default: Opaque)
- **Opaque:** User-defined data
- **kubernetes.io/dockerconfigjson:** Docker config
- **kubernetes.io/tls:** TLS certificate
- **kubernetes.io/service-account-token:** Service account token

**data:**
- Base64-encoded data (key-value pairs)
- **Keys:** String keys
- **Values:** Base64-encoded strings
- Must be base64-encoded

**stringData:**
- Plain text data (optional)
- Automatically base64-encoded
- Convenient for plain text input
- Merged into data field

#### Creating Secrets

**Method 1: Using YAML**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
  namespace: mysql
data:
  MYSQL_ROOT_PASSWORD: cm9vdAo=  # base64 encoded
```

```bash
kubectl apply -f secret.yaml
```

**Method 2: Using kubectl**

```bash
# Create from literal values (auto-encodes)
kubectl create secret generic app-secret \
  --from-literal=password=secret123 \
  --from-literal=api_key=abc123

# Create from file
kubectl create secret generic app-secret \
  --from-file=password.txt

# Create from directory
kubectl create secret generic app-secret \
  --from-file=/path/to/secrets

# Create TLS secret
kubectl create secret tls tls-secret \
  --cert=tls.crt \
  --key=tls.key

# Create Docker registry secret
kubectl create secret docker-registry regcred \
  --docker-server=registry.example.com \
  --docker-username=user \
  --docker-password=pass \
  --docker-email=user@example.com
```

**Method 3: Using stringData (Plain Text)**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
stringData:
  password: secret123  # Plain text, auto-encoded
  api_key: abc123
```

#### Encoding/Decoding Secrets

**Encode to Base64:**
```bash
# Encode string
echo -n "password123" | base64
# Output: cGFzc3dvcmQxMjM=

# Encode file
base64 -i password.txt -o password.b64
```

**Decode from Base64:**
```bash
# Decode string
echo "cGFzc3dvcmQxMjM=" | base64 -d
# Output: password123

# Decode from Secret
kubectl get secret app-secret -o jsonpath='{.data.password}' | base64 -d
```

#### Using Secrets in Pods

**Method 1: Environment Variables**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    env:
    - name: PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: password
    - name: API_KEY
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: api_key
```

**Method 2: All Keys as Environment Variables**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    envFrom:
    - secretRef:
        name: app-secret
```

**Method 3: Volume Mount (Secret File)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secrets
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: app-secret
```

**Method 4: Specific Keys as Files**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secrets
  volumes:
  - name: secret-volume
    secret:
      secretName: app-secret
      items:
      - key: password
        path: db-password
        mode: 0400  # Read-only for owner
```

#### Managing Secrets

```bash
# List Secrets
kubectl get secrets
kubectl get secret

# List Secrets in namespace
kubectl get secret -n <namespace>

# Get Secret details (values are hidden)
kubectl get secret <secret-name>

# Describe Secret (values are hidden)
kubectl describe secret <secret-name>

# View Secret data (base64-encoded)
kubectl get secret <secret-name> -o yaml

# Decode Secret value
kubectl get secret <secret-name> -o jsonpath='{.data.password}' | base64 -d

# Edit Secret
kubectl edit secret <secret-name>

# Delete Secret
kubectl delete secret <secret-name>
```

#### Secret Best Practices

1. **Use for sensitive data only** - Passwords, tokens, keys
2. **Enable encryption at rest** - Encrypt etcd
3. **Use RBAC** - Restrict access to Secrets
4. **Rotate regularly** - Change secrets periodically
5. **Don't commit to Git** - Never commit secrets
6. **Use external secret management** - For production (Vault, AWS Secrets Manager)
7. **Use stringData for convenience** - Easier than encoding
8. **Set appropriate file permissions** - When mounting as files

#### Example Reference

For a practical example of a Secret YAML file, check out:

- **[mysql/secrets.yml](./mysql/secrets.yml)** - Example Secret definition

This example demonstrates:
- Secret structure with data field
- Base64-encoded sensitive data (MYSQL_ROOT_PASSWORD)
- Database password pattern
- Namespace assignment

**To use this example:**
```bash
# Apply the Secret
kubectl apply -f mysql/secrets.yml

# View the Secret (values are hidden)
kubectl get secret -n mysql

# Check Secret details
kubectl describe secret mysql-secret -n mysql

# View Secret data (base64-encoded)
kubectl get secret mysql-secret -n mysql -o yaml

# Decode the password
kubectl get secret mysql-secret -n mysql -o jsonpath='{.data.MYSQL_ROOT_PASSWORD}' | base64 -d

# Use in Pod (example)
# Reference this Secret in Pod spec using secretKeyRef or envFrom
```

**Security Note:**
- The password in the example is base64-encoded "root"
- In production, use strong passwords
- Consider using external secret management systems
- Enable encryption at rest for etcd

#### Key Takeaways

1. **Secrets store sensitive data** - Passwords, tokens, keys
2. **Base64-encoded** - Not encrypted, just encoded
3. **Use RBAC** - Control access to Secrets
4. **Multiple usage methods** - Env vars, files, command args
5. **Namespace-scoped** - Belongs to a namespace
6. **Don't commit to Git** - Never version control secrets
7. **Enable encryption at rest** - For production
8. **Consider external management** - For enterprise use

---

### ConfigMap vs Secrets

| Aspect | ConfigMap | Secret |
|--------|-----------|--------|
| **Purpose** | Non-sensitive config | Sensitive data |
| **Encoding** | Plain text | Base64-encoded |
| **Size Limit** | 1MB | 1MB |
| **Use Case** | URLs, feature flags | Passwords, tokens |
| **Security** | No special protection | RBAC, encryption |
| **Visibility** | Visible in YAML | Hidden in describe |

**When to Use:**
- **ConfigMap:** Configuration data, URLs, feature flags, non-sensitive settings
- **Secret:** Passwords, API keys, tokens, certificates, any sensitive data

Both ConfigMap and Secrets are essential for managing configuration and sensitive data in Kubernetes applications.

</details>

---

<details>
<summary><h2>Resource Quotas and Limits</h2></summary>

## Resource Quotas and Limits

**Resource Quotas and Limits** are mechanisms in Kubernetes to control resource consumption. They ensure fair resource allocation and prevent any single application from consuming all cluster resources.

### Resource Management Overview

Kubernetes provides multiple levels of resource management:

1. **Container Level** - Requests and Limits for individual containers
2. **Pod Level** - Sum of all container resources
3. **Namespace Level** - Resource Quotas for entire namespace
4. **Cluster Level** - Total cluster capacity

### Resource Requests and Limits

**Requests** and **Limits** are specified at the container level and control CPU and memory allocation.

#### What are Requests and Limits?

**Requests:**
- **Guaranteed resources** - Minimum resources reserved for container
- **Scheduling decision** - Scheduler uses requests to place Pods
- **Guaranteed allocation** - Container gets at least this amount

**Limits:**
- **Maximum resources** - Container cannot exceed this
- **Enforcement** - Container is throttled/killed if exceeded
- **Protection** - Prevents resource exhaustion

**Simple Analogy:**
- **Request** = "I need at least 1GB RAM" (guaranteed)
- **Limit** = "I can use up to 2GB RAM" (maximum)

#### Resource Units

**CPU:**
- **1 CPU** = 1 vCPU/core = 1000m (millicores)
- Examples: `100m`, `0.5`, `1`, `2`
- Can be specified as: `100m`, `0.1`, `1`

**Memory:**
- Units: `Ki`, `Mi`, `Gi`, `Ti` (binary) or `K`, `M`, `G`, `T` (decimal)
- Examples: `128Mi`, `1Gi`, `512Mi`
- Always specify units (no bare numbers)

#### Resource Requests and Limits Diagram

```mermaid
graph TB
    Pod[Pod]
    
    Pod -->|Contains| C1[Container 1<br/>Request: 100m CPU, 128Mi<br/>Limit: 200m CPU, 256Mi]
    Pod -->|Contains| C2[Container 2<br/>Request: 200m CPU, 256Mi<br/>Limit: 500m CPU, 512Mi]
    
    Pod -->|Total| Total[Pod Total<br/>Request: 300m CPU, 384Mi<br/>Limit: 700m CPU, 768Mi]
    
    Scheduler[Scheduler] -->|Uses Requests| Total
    Scheduler -->|Places Pod on Node| Node[Node with<br/>Sufficient Resources]
    
    style Pod fill:#326ce5,color:#fff
    style C1 fill:#4fc3f7,color:#000
    style C2 fill:#4fc3f7,color:#000
    style Total fill:#90caf9,color:#000
    style Scheduler fill:#ff9800,color:#fff
    style Node fill:#90ee90,color:#000
```

#### Setting Resource Requests and Limits

**In Pod Spec:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

**In Deployment:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "200m"
```

#### Resource QoS Classes

Kubernetes assigns Quality of Service (QoS) classes based on resource settings:

**1. Guaranteed:**
- All containers have requests = limits
- Highest priority
- Last to be evicted

```yaml
resources:
  requests:
    memory: "128Mi"
    cpu: "200m"
  limits:
    memory: "128Mi"  # Same as request
    cpu: "200m"      # Same as request
```

**2. Burstable:**
- At least one container has request < limit
- Medium priority
- Evicted if BestEffort pods exist

```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "100m"
  limits:
    memory: "128Mi"  # Higher than request
    cpu: "200m"      # Higher than request
```

**3. BestEffort:**
- No requests or limits specified
- Lowest priority
- First to be evicted

```yaml
# No resources specified
```

#### Resource Enforcement

**CPU Limits:**
- Container throttled if exceeds limit
- CPU is compressible (can be throttled)
- Container continues running

**Memory Limits:**
- Container killed (OOMKilled) if exceeds limit
- Memory is incompressible
- Pod may be restarted

### Resource Quotas

**ResourceQuota** restricts the total resource consumption per namespace. It prevents a namespace from consuming all cluster resources.

#### What is a ResourceQuota?

A ResourceQuota:
- **Limits namespace resources** - Total CPU, memory, storage
- **Limits object counts** - Number of Pods, Services, etc.
- **Namespace-scoped** - Applies to entire namespace
- **Enforced** - Prevents exceeding limits

#### ResourceQuota Diagram

```mermaid
graph TB
    NS[Namespace: production]
    
    NS -->|Has| RQ[ResourceQuota<br/>CPU: 4 cores<br/>Memory: 8Gi<br/>Pods: 10]
    
    NS -->|Contains| Pod1[Pod 1<br/>Request: 500m, 1Gi]
    NS -->|Contains| Pod2[Pod 2<br/>Request: 500m, 1Gi]
    NS -->|Contains| Pod3[Pod 3<br/>Request: 1 core, 2Gi]
    
    Total[Total Used<br/>2 cores, 4Gi<br/>3 Pods]
    
    Pod1 --> Total
    Pod2 --> Total
    Pod3 --> Total
    
    Total -->|Checked Against| RQ
    
    RQ -->|Allows| Allow[Can Create More<br/>2 cores, 4Gi, 7 Pods Available]
    RQ -->|Blocks| Block[Rejects if<br/>Exceeds Limits]
    
    style NS fill:#326ce5,color:#fff
    style RQ fill:#d32f2f,color:#fff
    style Total fill:#90caf9,color:#000
    style Allow fill:#90ee90,color:#000
    style Block fill:#f44336,color:#fff
```

#### ResourceQuota Structure

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: production
spec:
  hard:
    # Compute resources
    requests.cpu: "4"
    requests.memory: 8Gi
    limits.cpu: "8"
    limits.memory: 16Gi
    
    # Storage resources
    requests.storage: 100Gi
    persistentvolumeclaims: "10"
    
    # Object counts
    pods: "10"
    services: "5"
    deployments.apps: "5"
    replicasets.apps: "10"
    secrets: "10"
    configmaps: "10"
```

#### ResourceQuota Spec Fields Explained

**apiVersion:**
- API version for ResourceQuota
- Required: `v1`

**kind:**
- Object type
- Required: `ResourceQuota`

**metadata:**
- Object metadata
- **name:** ResourceQuota name (required)
- **namespace:** Namespace (required)

**spec:**
- ResourceQuota specification
- **hard:** Hard limits (required)
  - **requests.cpu:** Total CPU requests
  - **requests.memory:** Total memory requests
  - **limits.cpu:** Total CPU limits
  - **limits.memory:** Total memory limits
  - **requests.storage:** Total storage requests
  - **persistentvolumeclaims:** Number of PVCs
  - **pods:** Number of Pods
  - **services:** Number of Services
  - **deployments.apps:** Number of Deployments
  - **replicasets.apps:** Number of ReplicaSets
  - **secrets:** Number of Secrets
  - **configmaps:** Number of ConfigMaps
  - And many more...

#### Creating ResourceQuota

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: production
spec:
  hard:
    requests.cpu: "4"
    requests.memory: 8Gi
    limits.cpu: "8"
    limits.memory: 16Gi
    pods: "10"
```

```bash
kubectl apply -f resourcequota.yaml
```

#### Managing ResourceQuota

```bash
# List ResourceQuotas
kubectl get resourcequotas
kubectl get quota

# List in namespace
kubectl get quota -n <namespace>

# Get ResourceQuota details
kubectl get quota <quota-name> -n <namespace>

# Describe ResourceQuota
kubectl describe quota <quota-name> -n <namespace>

# View ResourceQuota YAML
kubectl get quota <quota-name> -n <namespace> -o yaml

# Delete ResourceQuota
kubectl delete quota <quota-name> -n <namespace>
```

### LimitRange

**LimitRange** sets default resource requests and limits for Pods and containers in a namespace. It also sets min/max constraints.

#### What is a LimitRange?

A LimitRange:
- **Sets defaults** - Default requests/limits if not specified
- **Enforces min/max** - Validates resource requests/limits
- **Namespace-scoped** - Applies to entire namespace
- **Prevents issues** - Ensures all Pods have resources

#### LimitRange Diagram

```mermaid
graph TB
    NS[Namespace: production]
    
    NS -->|Has| LR[LimitRange<br/>Default: 100m CPU, 128Mi<br/>Min: 50m CPU, 64Mi<br/>Max: 2 CPU, 4Gi]
    
    Pod1[Pod Created<br/>No Resources Specified] -->|Gets| Default[Default Resources<br/>100m CPU, 128Mi]
    
    Pod2[Pod Created<br/>50m CPU, 64Mi] -->|Validated| Min[Within Min/Max<br/>Allowed]
    
    Pod3[Pod Created<br/>5 CPU, 10Gi] -->|Validated| Max[Exceeds Max<br/>Rejected]
    
    LR -->|Applies| Default
    LR -->|Validates| Min
    LR -->|Validates| Max
    
    style NS fill:#326ce5,color:#fff
    style LR fill:#ff9800,color:#fff
    style Default fill:#90ee90,color:#000
    style Min fill:#90ee90,color:#000
    style Max fill:#f44336,color:#fff
```

#### LimitRange Structure

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
  namespace: production
spec:
  limits:
  - default:
      memory: "512Mi"
      cpu: "500m"
    defaultRequest:
      memory: "256Mi"
      cpu: "250m"
    max:
      memory: "1Gi"
      cpu: "1"
    min:
      memory: "128Mi"
      cpu: "100m"
    type: Container
```

#### LimitRange Spec Fields Explained

**apiVersion:**
- API version for LimitRange
- Required: `v1`

**kind:**
- Object type
- Required: `LimitRange`

**metadata:**
- Object metadata
- **name:** LimitRange name (required)
- **namespace:** Namespace (required)

**spec:**
- LimitRange specification
- **limits:** List of limit rules
  - **type:** Resource type
    - **Container:** For containers
    - **Pod:** For entire Pod
    - **PersistentVolumeClaim:** For PVCs
  - **default:** Default limits (if not specified)
  - **defaultRequest:** Default requests (if not specified)
  - **max:** Maximum allowed
  - **min:** Minimum required
  - **maxLimitRequestRatio:** Max ratio of limit to request

#### Creating LimitRange

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
  namespace: production
spec:
  limits:
  - default:
      memory: "512Mi"
      cpu: "500m"
    defaultRequest:
      memory: "256Mi"
      cpu: "250m"
    max:
      memory: "1Gi"
      cpu: "1"
    min:
      memory: "128Mi"
      cpu: "100m"
    type: Container
```

```bash
kubectl apply -f limitrange.yaml
```

#### Managing LimitRange

```bash
# List LimitRanges
kubectl get limitranges
kubectl get limits

# List in namespace
kubectl get limits -n <namespace>

# Get LimitRange details
kubectl get limits <limitrange-name> -n <namespace>

# Describe LimitRange
kubectl describe limits <limitrange-name> -n <namespace>

# View LimitRange YAML
kubectl get limits <limitrange-name> -n <namespace> -o yaml

# Delete LimitRange
kubectl delete limits <limitrange-name> -n <namespace>
```

### How They Work Together

**Resource Management Hierarchy:**

```
Cluster Resources
    ↓
ResourceQuota (Namespace Level)
    ↓
LimitRange (Default/Min/Max)
    ↓
Pod Resources (Sum of Containers)
    ↓
Container Resources (Requests/Limits)
```

**Example Flow:**

1. **LimitRange** sets defaults: 100m CPU, 128Mi memory
2. **Pod created** without resources → Gets defaults from LimitRange
3. **ResourceQuota** checks: Total namespace resources < quota
4. **If exceeds quota** → Pod creation rejected
5. **If within quota** → Pod created with resources

### Best Practices

**Resource Requests and Limits:**

1. **Always set requests** - Helps scheduler place Pods
2. **Set appropriate limits** - Prevent resource exhaustion
3. **Requests ≤ Limits** - Limits should be >= requests
4. **Monitor usage** - Adjust based on actual usage
5. **Use meaningful units** - Be specific with units

**ResourceQuota:**

1. **Set per namespace** - Different quotas for different namespaces
2. **Start conservative** - Adjust based on needs
3. **Monitor usage** - Check quota utilization
4. **Include object counts** - Limit number of objects
5. **Review regularly** - Update as needs change

**LimitRange:**

1. **Set defaults** - Ensure all Pods have resources
2. **Set min/max** - Prevent too small/large resources
3. **Namespace-specific** - Different limits per namespace
4. **Use with ResourceQuota** - Works together
5. **Test limits** - Verify they work correctly

### Common Patterns

**Pattern 1: Development Namespace**

```yaml
# ResourceQuota - Generous limits
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-quota
  namespace: development
spec:
  hard:
    requests.cpu: "2"
    requests.memory: 4Gi
    limits.cpu: "4"
    limits.memory: 8Gi
    pods: "20"

---
# LimitRange - Small defaults
apiVersion: v1
kind: LimitRange
metadata:
  name: dev-limits
  namespace: development
spec:
  limits:
  - default:
      memory: "256Mi"
      cpu: "200m"
    defaultRequest:
      memory: "128Mi"
      cpu: "100m"
    type: Container
```

**Pattern 2: Production Namespace**

```yaml
# ResourceQuota - Strict limits
apiVersion: v1
kind: ResourceQuota
metadata:
  name: prod-quota
  namespace: production
spec:
  hard:
    requests.cpu: "8"
    requests.memory: 16Gi
    limits.cpu: "16"
    limits.memory: 32Gi
    pods: "10"
    deployments.apps: "5"

---
# LimitRange - Enforced limits
apiVersion: v1
kind: LimitRange
metadata:
  name: prod-limits
  namespace: production
spec:
  limits:
  - default:
      memory: "512Mi"
      cpu: "500m"
    defaultRequest:
      memory: "256Mi"
      cpu: "250m"
    max:
      memory: "2Gi"
      cpu: "2"
    min:
      memory: "128Mi"
      cpu: "100m"
    type: Container
```

### Troubleshooting

**Common Issues:**

1. **Pod creation fails:**
   ```bash
   # Check ResourceQuota
   kubectl describe quota -n <namespace>
   
   # Check usage
   kubectl get quota -n <namespace> -o yaml
   ```

2. **Resource limit exceeded:**
   ```bash
   # Check container limits
   kubectl describe pod <pod-name>
   
   # Check for OOMKilled
   kubectl get pod <pod-name>
   ```

3. **Scheduling failures:**
   ```bash
   # Check node resources
   kubectl describe node <node-name>
   
   # Check pod requests
   kubectl describe pod <pod-name>
   ```

### Key Takeaways

1. **Requests = Guaranteed resources** - Minimum allocation
2. **Limits = Maximum resources** - Cannot exceed
3. **ResourceQuota = Namespace limits** - Total resources per namespace
4. **LimitRange = Defaults and constraints** - Min/max/default values
5. **QoS classes** - Guaranteed, Burstable, BestEffort
6. **CPU is compressible** - Can be throttled
7. **Memory is incompressible** - Container killed if exceeded
8. **Always set resources** - Helps with scheduling and stability
9. **Monitor usage** - Adjust based on actual consumption
10. **Use together** - ResourceQuota + LimitRange for complete control

Resource Quotas and Limits are essential for managing resources in Kubernetes clusters, ensuring fair allocation and preventing resource exhaustion.

</details>

---

<details>
<summary><h2>Probes</h2></summary>

## Probes

**Probes** are health checks that Kubernetes performs on containers to determine their state. They help Kubernetes make decisions about container lifecycle and traffic routing.

### What are Probes?

**Simple Explanation:**
Probes are like **health checkups** for your containers. Kubernetes periodically checks:
- **Is the container alive?** - Should it be restarted?
- **Is the container ready?** - Can it receive traffic?
- **Has the container started?** - Is it still starting up?

**Why Use Probes?**
- **Self-healing** - Automatically restart unhealthy containers
- **Traffic management** - Only send traffic to ready containers
- **Startup handling** - Wait for slow-starting containers
- **Better reliability** - Catch issues before they affect users

**Analogy:**
Think of a restaurant:
- **Liveness Probe** = "Is the waiter still alive/working?" (restart if not)
- **Readiness Probe** = "Is the waiter ready to serve?" (don't send customers if not)
- **Startup Probe** = "Is the waiter still getting ready?" (wait before checking readiness)

### Types of Probes

Kubernetes provides three types of probes:

1. **Liveness Probe** - Is the container running?
2. **Readiness Probe** - Is the container ready to serve traffic?
3. **Startup Probe** - Has the container finished starting?

### Probe Types

Each probe can use one of three mechanisms:

1. **HTTP GET** - HTTP request to container
2. **TCP Socket** - TCP connection to container
3. **Exec** - Execute command in container

### Probes Overview Diagram

```mermaid
graph TB
    Pod[Pod with Container]
    
    Pod -->|Has| LP[Liveness Probe<br/>Is container alive?]
    Pod -->|Has| RP[Readiness Probe<br/>Is container ready?]
    Pod -->|Has| SP[Startup Probe<br/>Is container started?]
    
    LP -->|Fails| Restart[Restart Container]
    LP -->|Passes| Running[Container Running]
    
    RP -->|Fails| Remove[Remove from Service<br/>No Traffic]
    RP -->|Passes| Ready[Add to Service<br/>Receive Traffic]
    
    SP -->|Fails| Wait[Wait Longer]
    SP -->|Passes| Start[Start Liveness/Readiness]
    
    style Pod fill:#326ce5,color:#fff
    style LP fill:#d32f2f,color:#fff
    style RP fill:#ff9800,color:#fff
    style SP fill:#4caf50,color:#fff
    style Restart fill:#f44336,color:#fff
    style Ready fill:#90ee90,color:#000
```

---

### Liveness Probe

**Liveness Probe** determines if a container is running. If it fails, Kubernetes kills and restarts the container.

#### What is a Liveness Probe?

A Liveness Probe:
- **Checks if container is alive** - Is the application running?
- **Restarts on failure** - Kills and restarts container if probe fails
- **Prevents dead containers** - Removes containers that are stuck
- **Continuous monitoring** - Runs throughout container lifetime

**Use Case:**
- Application is running but stuck (deadlock, infinite loop)
- Application crashed but process still running
- Container needs restart to recover

#### Liveness Probe Diagram

```mermaid
graph LR
    Container[Container Running]
    
    Container -->|Every periodSeconds| LP[Liveness Probe<br/>Check Health]
    
    LP -->|Success| Continue[Continue Running]
    LP -->|Failure| Count[Failure Count++]
    
    Count -->|failureThreshold| Kill[Kill Container]
    Kill -->|Restart| Restart[Restart Container]
    
    Continue --> Container
    
    style Container fill:#4fc3f7,color:#000
    style LP fill:#d32f2f,color:#fff
    style Continue fill:#90ee90,color:#000
    style Kill fill:#f44336,color:#fff
    style Restart fill:#ff9800,color:#fff
```

#### Liveness Probe Configuration

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    livenessProbe:
      httpGet:
        path: /health
        port: 80
      initialDelaySeconds: 30
      periodSeconds: 10
      timeoutSeconds: 5
      successThreshold: 1
      failureThreshold: 3
```

#### Liveness Probe Parameters

**initialDelaySeconds:**
- Time to wait before first probe (default: 0)
- Give container time to start
- Example: `30` (wait 30 seconds)

**periodSeconds:**
- How often to perform probe (default: 10)
- Frequency of health checks
- Example: `10` (every 10 seconds)

**timeoutSeconds:**
- Probe timeout (default: 1)
- Maximum time to wait for response
- Example: `5` (5 seconds timeout)

**successThreshold:**
- Consecutive successes needed (default: 1)
- For liveness: must be 1
- Example: `1`

**failureThreshold:**
- Consecutive failures before action (default: 3)
- Number of failures before restart
- Example: `3` (restart after 3 failures)

#### Liveness Probe Examples

**Example 1: HTTP GET Probe**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: http-liveness
spec:
  containers:
  - name: app
    image: nginx:latest
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
        scheme: HTTP
        httpHeaders:
        - name: Custom-Header
          value: HealthCheck
      initialDelaySeconds: 30
      periodSeconds: 10
      failureThreshold: 3
```

**Example 2: TCP Socket Probe**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: tcp-liveness
spec:
  containers:
  - name: app
    image: nginx:latest
    livenessProbe:
      tcpSocket:
        port: 80
      initialDelaySeconds: 15
      periodSeconds: 20
      failureThreshold: 3
```

**Example 3: Exec Probe**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: exec-liveness
spec:
  containers:
  - name: app
    image: nginx:latest
    livenessProbe:
      exec:
        command:
        - /bin/sh
        - -c
        - "ps aux | grep nginx | grep -v grep"
      initialDelaySeconds: 30
      periodSeconds: 10
      failureThreshold: 3
```

---

### Readiness Probe

**Readiness Probe** determines if a container is ready to accept traffic. If it fails, the container is removed from Service endpoints.

#### What is a Readiness Probe?

A Readiness Probe:
- **Checks if container is ready** - Can it handle requests?
- **Removes from Service** - Container not included in load balancing
- **Doesn't restart** - Container keeps running, just no traffic
- **Traffic management** - Only ready containers receive traffic

**Use Case:**
- Application is starting but not ready
- Application is temporarily overloaded
- Application needs to load data before serving
- Database connection not ready

#### Readiness Probe Diagram

```mermaid
graph TB
    Service[Service]
    
    Service -->|Routes Traffic| Ready[Ready Pods Only]
    
    Pod1[Pod 1<br/>Readiness: Pass] -->|Included| Ready
    Pod2[Pod 2<br/>Readiness: Pass] -->|Included| Ready
    Pod3[Pod 3<br/>Readiness: Fail] -->|Excluded| NotReady[Not Ready<br/>No Traffic]
    
    RP[Readiness Probe] -->|Checks| Pod1
    RP -->|Checks| Pod2
    RP -->|Checks| Pod3
    
    Pod3 -->|Becomes Ready| Ready
    
    style Service fill:#326ce5,color:#fff
    style Ready fill:#90ee90,color:#000
    style NotReady fill:#ff9800,color:#fff
    style RP fill:#ff9800,color:#fff
```

#### Readiness Probe Configuration

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: readiness-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    readinessProbe:
      httpGet:
        path: /ready
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
      timeoutSeconds: 3
      successThreshold: 1
      failureThreshold: 3
```

#### Readiness Probe Examples

**Example 1: HTTP GET Probe**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: http-readiness
spec:
  containers:
  - name: app
    image: nginx:latest
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 5
      failureThreshold: 3
```

**Example 2: TCP Socket Probe**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: tcp-readiness
spec:
  containers:
  - name: app
    image: nginx:latest
    readinessProbe:
      tcpSocket:
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
```

**Example 3: Exec Probe**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: exec-readiness
spec:
  containers:
  - name: app
    image: nginx:latest
    readinessProbe:
      exec:
        command:
        - /bin/sh
        - -c
        - "curl -f http://localhost:80/ready || exit 1"
      initialDelaySeconds: 5
      periodSeconds: 5
```

---

### Startup Probe

**Startup Probe** determines if a container has finished starting. It disables liveness and readiness probes until it succeeds.

#### What is a Startup Probe?

A Startup Probe:
- **Checks if container started** - Has initialization completed?
- **Disables other probes** - Liveness/readiness wait until startup succeeds
- **Handles slow starts** - For applications that take time to start
- **One-time check** - Once successful, liveness/readiness take over

**Use Case:**
- Applications with long startup times
- Applications that need initialization
- Legacy applications
- Applications loading large datasets

#### Startup Probe Diagram

```mermaid
graph TB
    Container[Container Starting]
    
    Container -->|Start| SP[Startup Probe<br/>Is container started?]
    
    SP -->|Fails| Wait[Wait periodSeconds<br/>Try Again]
    Wait --> SP
    
    SP -->|Succeeds| Enable[Enable Liveness<br/>Enable Readiness]
    
    Enable --> LP[Liveness Probe<br/>Active]
    Enable --> RP[Readiness Probe<br/>Active]
    
    style Container fill:#4fc3f7,color:#000
    style SP fill:#4caf50,color:#fff
    style Wait fill:#ff9800,color:#fff
    style LP fill:#d32f2f,color:#fff
    style RP fill:#ff9800,color:#fff
```

#### Startup Probe Configuration

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: startup-pod
spec:
  containers:
  - name: app
    image: nginx:latest
    startupProbe:
      httpGet:
        path: /startup
        port: 80
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 5
      successThreshold: 1
      failureThreshold: 30
    livenessProbe:
      httpGet:
        path: /health
        port: 80
      periodSeconds: 10
    readinessProbe:
      httpGet:
        path: /ready
        port: 80
      periodSeconds: 5
```

#### Startup Probe Examples

**Example 1: HTTP GET Probe**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: http-startup
spec:
  containers:
  - name: app
    image: nginx:latest
    startupProbe:
      httpGet:
        path: /startup
        port: 8080
      initialDelaySeconds: 10
      periodSeconds: 10
      failureThreshold: 30  # Allow up to 5 minutes (30 * 10s)
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
      periodSeconds: 10
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
      periodSeconds: 5
```

**Example 2: Exec Probe for Database**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: db-startup
spec:
  containers:
  - name: mysql
    image: mysql:8.0
    startupProbe:
      exec:
        command:
        - /bin/sh
        - -c
        - "mysqladmin ping -h localhost"
      initialDelaySeconds: 30
      periodSeconds: 10
      failureThreshold: 30
    livenessProbe:
      exec:
        command:
        - /bin/sh
        - -c
        - "mysqladmin ping -h localhost"
      periodSeconds: 30
```

---

### Probe Types Explained

#### 1. HTTP GET Probe

**What it does:**
- Sends HTTP GET request to container
- Checks HTTP status code
- Success if status code is 200-399

**Configuration:**
```yaml
httpGet:
  path: /health
  port: 80
  scheme: HTTP  # or HTTPS
  httpHeaders:
  - name: Custom-Header
    value: Value
```

**Use Case:**
- Web applications
- REST APIs
- Applications with HTTP endpoints

#### 2. TCP Socket Probe

**What it does:**
- Attempts TCP connection to container
- Success if connection succeeds
- Doesn't send any data

**Configuration:**
```yaml
tcpSocket:
  port: 80
```

**Use Case:**
- Applications without HTTP endpoints
- Database connections
- Custom protocols

#### 3. Exec Probe

**What it does:**
- Executes command in container
- Success if command exits with code 0
- Failure if command exits with non-zero code

**Configuration:**
```yaml
exec:
  command:
  - /bin/sh
  - -c
  - "command to execute"
```

**Use Case:**
- Custom health check logic
- Script-based checks
- Complex validation

### Complete Probe Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: complete-probe-example
spec:
  containers:
  - name: app
    image: nginx:latest
    ports:
    - containerPort: 80
    
    # Startup Probe - Wait for app to start
    startupProbe:
      httpGet:
        path: /startup
        port: 80
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 5
      failureThreshold: 30  # 5 minutes max
    
    # Liveness Probe - Is app alive?
    livenessProbe:
      httpGet:
        path: /health
        port: 80
      initialDelaySeconds: 30
      periodSeconds: 10
      timeoutSeconds: 5
      failureThreshold: 3  # Restart after 3 failures
    
    # Readiness Probe - Is app ready?
    readinessProbe:
      httpGet:
        path: /ready
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
      timeoutSeconds: 3
      failureThreshold: 3  # Remove from service after 3 failures
```

### Probe Timing Diagram

```mermaid
graph LR
    Start[Container Starts] -->|0-10s| Wait[Initial Delay]
    Wait -->|10s| SP[Startup Probe<br/>Active]
    SP -->|10-60s| Check[Startup Checks<br/>Every 10s]
    Check -->|Success| Enable[Enable Liveness<br/>Enable Readiness]
    Enable -->|60s+| LP[Liveness Probe<br/>Every 10s]
    Enable -->|60s+| RP[Readiness Probe<br/>Every 5s]
    
    style Start fill:#4fc3f7,color:#000
    style Wait fill:#ff9800,color:#fff
    style SP fill:#4caf50,color:#fff
    style Enable fill:#90ee90,color:#000
    style LP fill:#d32f2f,color:#fff
    style RP fill:#ff9800,color:#fff
```

### Probe Best Practices

**1. Use All Three Probes**
```yaml
startupProbe:    # For slow-starting apps
livenessProbe:   # To restart dead containers
readinessProbe:  # To manage traffic
```

**2. Set Appropriate Delays**
```yaml
startupProbe:
  initialDelaySeconds: 10  # Wait for startup
livenessProbe:
  initialDelaySeconds: 30  # Wait longer for liveness
readinessProbe:
  initialDelaySeconds: 5   # Check readiness quickly
```

**3. Use HTTP for Web Apps**
```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
```

**4. Use TCP for Non-HTTP**
```yaml
livenessProbe:
  tcpSocket:
    port: 3306
```

**5. Set Reasonable Thresholds**
```yaml
startupProbe:
  failureThreshold: 30  # Allow time for startup
livenessProbe:
  failureThreshold: 3   # Restart quickly if dead
readinessProbe:
  failureThreshold: 3   # Remove from service quickly
```

**6. Use Startup Probe for Slow Apps**
- Prevents premature restarts
- Allows long initialization
- Disables liveness/readiness until ready

**7. Make Probes Lightweight**
- Don't do heavy operations
- Keep response times fast
- Use dedicated health endpoints

**8. Test Your Probes**
- Verify they work correctly
- Test failure scenarios
- Monitor probe behavior

### Common Patterns

**Pattern 1: Web Application**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-app
spec:
  containers:
  - name: app
    image: web-app:latest
    startupProbe:
      httpGet:
        path: /startup
        port: 8080
      failureThreshold: 30
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
      periodSeconds: 10
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
      periodSeconds: 5
```

**Pattern 2: Database**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: database
spec:
  containers:
  - name: mysql
    image: mysql:8.0
    startupProbe:
      exec:
        command:
        - mysqladmin
        - ping
        - -h
        - localhost
      failureThreshold: 30
    livenessProbe:
      exec:
        command:
        - mysqladmin
        - ping
        - -h
        - localhost
      periodSeconds: 30
    readinessProbe:
      exec:
        command:
        - mysqladmin
        - ping
        - -h
        - localhost
      periodSeconds: 10
```

**Pattern 3: Simple Application**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-app
spec:
  containers:
  - name: app
    image: app:latest
    livenessProbe:
      tcpSocket:
        port: 8080
      periodSeconds: 10
    readinessProbe:
      tcpSocket:
        port: 8080
      periodSeconds: 5
```

### Troubleshooting Probes

**Common Issues:**

1. **Container restarts frequently:**
   ```bash
   # Check liveness probe
   kubectl describe pod <pod-name>
   
   # Check probe logs
   kubectl logs <pod-name>
   
   # Verify health endpoint
   kubectl exec <pod-name> -- curl http://localhost:80/health
   ```

2. **Container not receiving traffic:**
   ```bash
   # Check readiness probe
   kubectl describe pod <pod-name>
   
   # Check endpoints
   kubectl get endpoints <service-name>
   
   # Verify ready endpoint
   kubectl exec <pod-name> -- curl http://localhost:80/ready
   ```

3. **Container takes too long to start:**
   ```bash
   # Add startup probe
   # Increase failureThreshold
   # Check application logs
   ```

### Key Takeaways

1. **Liveness Probe** - Restarts container if unhealthy
2. **Readiness Probe** - Controls traffic routing
3. **Startup Probe** - Handles slow-starting applications
4. **Three probe types** - HTTP GET, TCP Socket, Exec
5. **Set appropriate delays** - Give containers time to start
6. **Use all three probes** - For best reliability
7. **Make probes lightweight** - Fast response times
8. **Test your probes** - Verify they work correctly
9. **Monitor probe behavior** - Check for issues
10. **Startup probe disables others** - Until it succeeds

Probes are essential for maintaining application health and reliability in Kubernetes. They enable self-healing and intelligent traffic management.

</details>

---

<details>
<summary><h2>Autoscaling</h2></summary>

## Autoscaling

**Autoscaling** in Kubernetes allows your applications and infrastructure to automatically adjust resources based on demand. This ensures optimal performance while minimizing costs.

### What is Autoscaling?

**Simple Explanation:**
Autoscaling automatically adjusts resources (Pods, CPU, memory, or nodes) based on current demand. It's like having a smart system that adds more servers when traffic increases and removes them when traffic decreases.

**Why We Need Autoscaling:**
- **Variable Workloads:** Traffic and resource needs change over time
- **Cost Optimization:** Pay only for what you use
- **Performance:** Ensure applications have enough resources
- **Efficiency:** Automatically handle scaling decisions

**Types of Autoscaling in Kubernetes:**
1. **HPA (Horizontal Pod Autoscaler)** - Scales number of Pods
2. **VPA (Vertical Pod Autoscaler)** - Adjusts CPU/memory requests/limits
3. **KEDA (Kubernetes Event-Driven Autoscaling)** - Event-driven scaling
4. **Cluster Autoscaler** - Scales cluster nodes (traditional)
5. **Karpenter** - High-performance node autoscaler (modern alternative)

### Autoscaling Overview Diagram

```mermaid
graph TB
    subgraph "Autoscaling Types"
        HPA[HPA<br/>Horizontal Pod Autoscaler<br/>Scales Pods]
        VPA[VPA<br/>Vertical Pod Autoscaler<br/>Adjusts Resources]
        KEDA[KEDA<br/>Event-Driven Autoscaling<br/>Event-Based Scaling]
        CA[Cluster Autoscaler<br/>Scales Nodes]
        Karpenter[Karpenter<br/>High-Performance<br/>Node Autoscaler]
    end
    
    subgraph "Metrics Sources"
        Metrics[Metrics Server<br/>CPU/Memory Metrics]
        Prometheus[Prometheus<br/>Custom Metrics]
        Events[Event Sources<br/>Queue/Kafka/etc]
    end
    
    subgraph "Targets"
        Pods[Pods<br/>Application Instances]
        Resources[Resources<br/>CPU/Memory]
        Nodes[Nodes<br/>Cluster Nodes]
    end
    
    HPA --> Metrics
    HPA --> Prometheus
    HPA --> Pods
    
    VPA --> Metrics
    VPA --> Resources
    
    KEDA --> Events
    KEDA --> Pods
    
    CA --> Nodes
    Karpenter --> Nodes
    
    style HPA fill:#326ce5,color:#fff
    style VPA fill:#326ce5,color:#fff
    style KEDA fill:#326ce5,color:#fff
    style CA fill:#326ce5,color:#fff
    style Karpenter fill:#326ce5,color:#fff
```

### When to Use Which Autoscaler?

| Autoscaler | Use Case | What It Scales | Best For |
|------------|----------|----------------|----------|
| **HPA** | CPU/Memory/ custom metrics high | Number of Pods | Stateless applications, web services |
| **VPA** | Resource requests need adjustment | CPU/Memory requests/limits | Applications with varying resource needs |
| **KEDA** | Event-driven workloads | Number of Pods based on events | Queue workers, event processors |
| **Cluster Autoscaler** | Cluster needs more nodes | Number of nodes | When Pods can't be scheduled (traditional) |
| **Karpenter** | Fast, efficient node provisioning | Number of nodes | AWS EKS, fast scaling, cost optimization |

### Key Takeaways

1. **Autoscaling optimizes resources** - Automatically adjusts to demand
2. **Multiple autoscalers available** - Choose based on your needs
3. **HPA for horizontal scaling** - Most common for Pod scaling
4. **VPA for resource optimization** - Adjusts CPU/memory per Pod
5. **KEDA for event-driven scaling** - Based on external events
6. **Cluster Autoscaler for infrastructure** - Traditional node autoscaler
7. **Karpenter for modern infrastructure** - Fast, efficient node autoscaler
8. **Can use multiple together** - Combine for comprehensive scaling

---

## HPA - Horizontal Pod Autoscaler

**Horizontal Pod Autoscaler (HPA)** automatically scales the number of Pod replicas based on observed CPU utilization, memory usage, or custom metrics.

### What is HPA?

**Simple Explanation:**
HPA automatically adds or removes Pod replicas based on metrics like CPU or memory usage. If your application is using too much CPU, HPA creates more Pods to share the load. If usage is low, it removes Pods to save resources.

**Simple Analogy:**
Think of HPA as an **automatic staffing manager**:
- Monitors how busy workers (Pods) are
- Hires more workers when busy (high CPU/memory)
- Lets workers go when not busy (low CPU/memory)
- Ensures optimal staffing levels

### Why Do We Need HPA?

**The Problem:**
- Traffic varies throughout the day
- Manual scaling is time-consuming
- Over-provisioning wastes resources
- Under-provisioning causes poor performance

**The Solution:**
- HPA automatically adjusts Pod count
- Responds to traffic changes in real-time
- Optimizes resource usage
- Maintains performance under load

### How HPA Works

**HPA Workflow:**
```
1. HPA collects metrics (CPU, memory, custom)
   ↓
2. Compares current metrics to target
   ↓
3. Calculates desired replica count
   ↓
4. Updates Deployment/ReplicaSet
   ↓
5. Controller creates/deletes Pods
   ↓
6. Repeats every 15 seconds (default)
```

**Scaling Decision:**
```
desiredReplicas = ceil[currentReplicas * (currentMetricValue / desiredMetricValue)]
```

**Example:**
```
Current: 2 Pods, CPU: 80%, Target: 50%
Desired: ceil[2 * (80/50)] = ceil[3.2] = 4 Pods
Result: Scale up to 4 Pods
```

### HPA Architecture Diagram

```mermaid
graph TB
    MS[Metrics Server] --> HPA[HPA Controller]
    PM[Prometheus] --> HPA
    HPA --> DEP[Deployment]
    DEP --> RS[ReplicaSet]
    RS --> PODS[Pods]
```

### HPA YAML Structure

**Basic HPA Structure:**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: <hpa-name>
  namespace: <namespace>
spec:
  scaleTargetRef:        # What to scale
    apiVersion: apps/v1
    kind: Deployment
    name: <deployment-name>
  minReplicas: <number>   # Minimum Pods
  maxReplicas: <number>   # Maximum Pods
  metrics:                # Metrics to track
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: <percentage>
```

**Key Fields Explained:**

**1. scaleTargetRef:**
- **apiVersion:** API version of target resource
- **kind:** Type of resource (Deployment, ReplicaSet, StatefulSet)
- **name:** Name of the resource to scale

**2. minReplicas:**
- Minimum number of Pod replicas
- HPA will never scale below this
- Default: 1

**3. maxReplicas:**
- Maximum number of Pod replicas
- HPA will never scale above this
- Required field

**4. metrics:**
- List of metrics to track
- Can track multiple metrics
- Types: Resource, Pods, Object, External

**5. behavior (optional):**
- Controls scaling behavior
- Scale up/down policies
- Stabilization windows

### HPA Metrics Types

**1. Resource Metrics:**
- CPU utilization
- Memory utilization
- Requires Metrics Server

**2. Pod Metrics:**
- Average metric value across all Pods
- Custom metrics per Pod

**3. Object Metrics:**
- Metrics from another object
- Example: Ingress requests per second

**4. External Metrics:**
- Metrics from outside Kubernetes
- Example: Queue length

### Creating HPA

**Example: HPA for CPU and Memory**

> **📁 Reference:** See example YAML file: [`apache/hpa.yaml`](../apache/hpa.yaml)

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: apache-hpa
  namespace: apache
spec:
  scaleTargetRef:
    kind: Deployment
    name: apache-deployment
    apiVersion: apps/v1
  minReplicas: 1
  maxReplicas: 5
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: 80
```

**Commands:**
```bash
# Create HPA
kubectl apply -f apache/hpa.yaml

# View HPA
kubectl get hpa -n apache

# Describe HPA
kubectl describe hpa apache-hpa -n apache

# Watch HPA in real-time
kubectl get hpa -n apache -w
```

### HPA Behavior Configuration

**Scale Up/Down Policies:**
```yaml
spec:
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 50
        periodSeconds: 60
    scaleUp:
      stabilizationWindowSeconds: 0
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
      - type: Pods
        value: 4
        periodSeconds: 15
      selectPolicy: Max
```

**Behavior Options:**
- **stabilizationWindowSeconds:** Wait time before scaling
- **policies:** Scaling rules (Percent or Pods)
- **selectPolicy:** Min, Max, or Disabled

### HPA Best Practices

**1. Set Appropriate Limits:**
- Set minReplicas based on minimum load
- Set maxReplicas based on maximum expected load
- Consider cost implications

**2. Configure Resource Requests:**
- HPA needs resource requests to calculate utilization
- Without requests, HPA can't determine CPU/memory usage

**3. Use Multiple Metrics:**
- Combine CPU and memory metrics
- Add custom metrics for better scaling decisions

**4. Configure Behavior:**
- Set appropriate stabilization windows
- Prevent rapid scaling up/down
- Use policies to control scaling speed

**5. Monitor HPA:**
- Watch HPA decisions
- Check metrics availability
- Monitor scaling events

**6. Test Scaling:**
- Test scale-up scenarios
- Test scale-down scenarios
- Verify metrics collection

### HPA Troubleshooting

**HPA Not Scaling:**
```bash
# Check HPA status
kubectl describe hpa <hpa-name> -n <namespace>

# Check metrics availability
kubectl top pods -n <namespace>

# Check Metrics Server
kubectl get deployment metrics-server -n kube-system
```

**Common Issues:**
1. **Metrics not available:**
   - Install Metrics Server
   - Check Metrics Server logs

2. **HPA shows "unknown":**
   - Metrics Server not running
   - Resource requests not set

3. **Not scaling as expected:**
   - Check target utilization
   - Verify metrics are being collected
   - Check min/max replicas

### Key Takeaways

1. **HPA scales Pods horizontally** - Adds/removes Pod replicas
2. **Based on metrics** - CPU, memory, or custom metrics
3. **Requires Metrics Server** - For resource metrics
4. **Works with Deployments** - And ReplicaSets, StatefulSets
5. **Configurable behavior** - Control scaling speed and policies
6. **Set resource requests** - Required for resource-based scaling
7. **Monitor and test** - Ensure HPA works as expected

---

## VPA - Vertical Pod Autoscaler

**Vertical Pod Autoscaler (VPA)** automatically adjusts CPU and memory requests and limits for Pods based on historical usage. Unlike HPA which scales horizontally, VPA scales vertically by adjusting resource allocations.

### What is VPA?

**Simple Explanation:**
VPA automatically adjusts the CPU and memory requests and limits for your Pods based on how much they actually use. If a Pod consistently uses more CPU than requested, VPA increases the request. If it uses less, VPA decreases it.

**Simple Analogy:**
Think of VPA as a **smart resource allocator**:
- Monitors how much resources Pods actually use
- Adjusts resource allocations (CPU/memory) per Pod
- Optimizes resource requests and limits
- Like adjusting a worker's desk size based on their needs

**Key Difference from HPA:**
- **HPA:** Changes number of Pods (horizontal scaling)
- **VPA:** Changes resources per Pod (vertical scaling)

### Why Do We Need VPA?

**The Problem:**
- Hard to predict resource needs
- Over-provisioning wastes resources
- Under-provisioning causes OOM kills
- Manual adjustment is time-consuming

**The Solution:**
- VPA analyzes historical usage
- Automatically adjusts requests/limits
- Prevents OOM kills
- Optimizes resource allocation

### How VPA Works

**VPA Components:**
1. **Recommender:** Analyzes usage and recommends values
2. **Updater:** Updates running Pods (in Auto mode)
3. **Admission Controller:** Sets requests/limits for new Pods

**VPA Workflow:**
```
1. VPA Recommender analyzes Pod resource usage
   ↓
2. Generates recommendations for requests/limits
   ↓
3. VPA Updater (Auto mode) evicts Pods with new values
   ↓
4. VPA Admission Controller sets values for new Pods
   ↓
5. Pods restart with optimized resources
```

**VPA Modes:**
- **Off:** Only provides recommendations
- **Initial:** Sets resources only when Pods are created
- **Auto:** Automatically updates running Pods (evicts and recreates)
- **Recreate:** Similar to Auto but waits for Pod termination

### VPA Architecture Diagram

```mermaid
graph TB
    HIST[Historical Metrics] --> REC[Recommender]
    REC --> VPA[VPA Controller]
    VPA --> UPD[Updater]
    VPA --> AC[Admission Controller]
    UPD --> POD1[Pod 1]
    UPD --> POD2[Pod 2]
    AC --> POD3[Pod 3]
```

### VPA YAML Structure

**Basic VPA Structure:**
```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: <vpa-name>
  namespace: <namespace>
spec:
  targetRef:              # What to scale
    apiVersion: apps/v1
    kind: Deployment
    name: <deployment-name>
  updatePolicy:           # How to update
    updateMode: <mode>    # Off, Initial, Auto, Recreate
  resourcePolicy:         # Resource constraints (optional)
    containerPolicies:
    - containerName: <container-name>
      minAllowed:
        cpu: <amount>
        memory: <amount>
      maxAllowed:
        cpu: <amount>
        memory: <amount>
```

**Key Fields Explained:**

**1. targetRef:**
- **apiVersion:** API version of target resource
- **kind:** Type of resource (Deployment, ReplicaSet, StatefulSet)
- **name:** Name of the resource to scale

**2. updatePolicy:**
- **updateMode:** How VPA updates Pods
  - **Off:** Only recommendations, no updates
  - **Initial:** Set resources only at Pod creation
  - **Auto:** Automatically update running Pods
  - **Recreate:** Update by recreating Pods

**3. resourcePolicy (optional):**
- **containerPolicies:** Per-container resource constraints
- **minAllowed:** Minimum CPU/memory
- **maxAllowed:** Maximum CPU/memory

### Creating VPA

**Example: VPA with Auto Mode**

> **📁 Reference:** See example YAML file: [`apache/vpa.yaml`](../apache/vpa.yaml)

```yaml
kind: VerticalPodAutoscaler
apiVersion: autoscaling.k8s.io/v1
metadata:
  name: apache-vpa
  namespace: apache
spec:
  targetRef:
    name: apache-deployment
    apiVersion: apps/v1
    kind: Deployment
  updatePolicy:
    updateMode: "Auto"
```

**Example: VPA with Resource Constraints**
```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: apache-vpa
  namespace: apache
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: apache-deployment
  updatePolicy:
    updateMode: "Auto"
  resourcePolicy:
    containerPolicies:
    - containerName: apache
      minAllowed:
        cpu: 100m
        memory: 128Mi
      maxAllowed:
        cpu: 2
        memory: 4Gi
```

**Commands:**
```bash
# Install VPA (if not installed)
# Follow VPA installation guide for your cluster

# Create VPA
kubectl apply -f apache/vpa.yaml

# View VPA
kubectl get vpa -n apache

# Describe VPA (see recommendations)
kubectl describe vpa apache-vpa -n apache

# View VPA recommendations
kubectl get vpa apache-vpa -n apache -o yaml
```

### VPA Update Modes Explained

**1. Off Mode:**
- VPA only provides recommendations
- No automatic updates
- Use for analysis and planning

**2. Initial Mode:**
- Sets resources only when Pods are created
- Doesn't update running Pods
- Safe for production

**3. Auto Mode:**
- Automatically updates running Pods
- Evicts Pods and recreates with new resources
- Most aggressive, use carefully

**4. Recreate Mode:**
- Similar to Auto but waits for Pod termination
- More controlled than Auto
- Good for stateful workloads

### VPA Best Practices

**1. Start with Off Mode:**
- Analyze recommendations first
- Understand resource needs
- Plan resource allocation

**2. Use Initial Mode for Production:**
- Safer than Auto mode
- Sets resources for new Pods
- No disruption to running Pods

**3. Set Resource Constraints:**
- Use minAllowed to prevent under-provisioning
- Use maxAllowed to prevent over-provisioning
- Protects against extreme recommendations

**4. Monitor VPA Recommendations:**
- Review recommendations regularly
- Adjust constraints based on observations
- Verify recommendations make sense

**5. Don't Use with HPA (CPU/Memory):**
- VPA and HPA can conflict
- Use VPA for resource optimization
- Use HPA for scaling Pod count

**6. Test in Non-Production:**
- Test VPA behavior first
- Understand update impact
- Verify resource adjustments

### VPA Limitations

**1. Pod Disruption:**
- Auto/Recreate modes evict Pods
- Causes temporary unavailability
- Not suitable for all workloads

**2. Stateful Workloads:**
- VPA can disrupt stateful applications
- Use carefully with StatefulSets
- Consider Initial mode only

**3. HPA Conflicts:**
- Don't use VPA with HPA on same metrics
- Can cause scaling conflicts
- Use VPA for optimization, HPA for scaling

**4. Requires VPA Installation:**
- Not included in standard Kubernetes
- Need to install VPA components
- Additional maintenance overhead

### Key Takeaways

1. **VPA adjusts resources vertically** - Changes CPU/memory per Pod
2. **Based on historical usage** - Analyzes past resource consumption
3. **Multiple update modes** - Off, Initial, Auto, Recreate
4. **Can set constraints** - Min/max allowed resources
5. **Don't use with HPA** - Can cause conflicts
6. **Causes Pod disruption** - In Auto/Recreate modes
7. **Requires VPA installation** - Not standard Kubernetes

---

## KEDA - Kubernetes Event-Driven Autoscaling

**KEDA (Kubernetes Event-Driven Autoscaling)** is a Kubernetes-based event-driven autoscaling component that can scale workloads based on events from various sources like message queues, databases, and cloud services.

### What is KEDA?

**Simple Explanation:**
KEDA automatically scales Pods based on events from external systems like message queues, databases, or cloud services. If there are many messages in a queue, KEDA scales up Pods to process them. When the queue is empty, it scales down.

**Simple Analogy:**
Think of KEDA as an **event-driven staffing manager**:
- Monitors external events (queue length, database connections, etc.)
- Scales workers (Pods) based on event volume
- Responds to real-time event changes
- Like hiring workers when orders come in

**Key Difference:**
- **HPA:** Scales based on CPU/memory metrics
- **KEDA:** Scales based on external events

### Why Do We Need KEDA?

**The Problem:**
- Event-driven workloads need event-based scaling
- CPU/memory metrics don't reflect event volume
- Need to scale based on queue length, message count, etc.
- HPA can't access external event sources

**The Solution:**
- KEDA connects to event sources
- Scales based on event metrics
- Supports many event sources
- Provides event-driven autoscaling

### How KEDA Works

**KEDA Components:**
1. **ScaledObject:** Defines what to scale and how
2. **Scalers:** Connect to event sources (queues, databases, etc.)
3. **Metrics Adapter:** Exposes metrics to HPA
4. **Controller:** Manages scaling decisions

**KEDA Workflow:**
```
1. KEDA Scaler connects to event source (e.g., RabbitMQ)
   ↓
2. Monitors event metrics (e.g., queue length)
   ↓
3. KEDA Metrics Adapter exposes metrics to HPA
   ↓
4. HPA scales Pods based on metrics
   ↓
5. Pods process events
   ↓
6. KEDA scales down when events decrease
```

### KEDA Architecture Diagram

```mermaid
graph TB
    QUEUE[Message Queue] --> SCALER[Scalers]
    DB[Database] --> SCALER
    CLOUD[Cloud Services] --> SCALER
    SCALER --> MA[Metrics Adapter]
    SO[ScaledObject] --> KEDA[KEDA Controller]
    KEDA --> SCALER
    MA --> HPA[HPA]
    HPA --> DEP[Deployment]
    DEP --> PODS[Pods]
```

### KEDA ScaledObject YAML Structure

**Basic ScaledObject Structure:**
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: <scaled-object-name>
  namespace: <namespace>
spec:
  scaleTargetRef:        # What to scale
    name: <deployment-name>
  minReplicaCount: <number>
  maxReplicaCount: <number>
  triggers:              # Event sources
    - type: <scaler-type>
      metadata:
        # Scaler-specific configuration
```

**Key Fields Explained:**

**1. scaleTargetRef:**
- **name:** Name of Deployment to scale
- Can also reference other workload types

**2. minReplicaCount:**
- Minimum number of Pod replicas
- Default: 0 (scale to zero)

**3. maxReplicaCount:**
- Maximum number of Pod replicas
- Required field

**4. triggers:**
- List of event sources
- Each trigger defines a scaler type and configuration
- Can have multiple triggers

### KEDA Supported Scalers

**Message Queues:**
- RabbitMQ
- Apache Kafka
- AWS SQS
- Azure Service Bus
- Redis Streams
- NATS Streaming

**Databases:**
- PostgreSQL
- MySQL
- MongoDB
- InfluxDB

**Cloud Services:**
- AWS Kinesis
- Azure Event Hubs
- GCP Pub/Sub

**Others:**
- Prometheus
- Cron
- External (custom)

### Creating KEDA ScaledObject

**Example: RabbitMQ Scaler**
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: rabbitmq-scaledobject
  namespace: default
spec:
  scaleTargetRef:
    name: worker-deployment
  minReplicaCount: 0
  maxReplicaCount: 10
  triggers:
    - type: rabbitmq
      metadata:
        queueName: tasks
        queueLength: '5'
        host: amqp://guest:password@rabbitmq.default.svc.cluster.local:5672/
```

**Example: Kafka Scaler**
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: kafka-scaledobject
  namespace: default
spec:
  scaleTargetRef:
    name: kafka-consumer
  minReplicaCount: 1
  maxReplicaCount: 20
  triggers:
    - type: kafka
      metadata:
        bootstrapServers: kafka:9092
        consumerGroup: my-group
        topic: events
        lagThreshold: '10'
```

**Example: Cron Scaler (Scheduled Scaling)**
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: cron-scaledobject
  namespace: default
spec:
  scaleTargetRef:
    name: batch-job
  minReplicaCount: 0
  maxReplicaCount: 5
  triggers:
    - type: cron
      metadata:
        timezone: Etc/UTC
        start: "0 9 * * *"    # 9 AM daily
        end: "0 17 * * *"      # 5 PM daily
        desiredReplicas: "3"
```

**Commands:**
```bash
# Install KEDA (if not installed)
# Follow KEDA installation guide

# Create ScaledObject
kubectl apply -f scaledobject.yaml

# View ScaledObjects
kubectl get scaledobject

# Describe ScaledObject
kubectl describe scaledobject <name>

# View HPA created by KEDA
kubectl get hpa
```

### KEDA Best Practices

**1. Set Appropriate Replica Counts:**
- Use minReplicaCount: 0 for scale-to-zero
- Set maxReplicaCount based on capacity
- Consider cost implications

**2. Configure Lag Thresholds:**
- Set appropriate lag thresholds for queues
- Balance between responsiveness and cost
- Monitor scaling behavior

**3. Use Multiple Triggers:**
- Combine multiple event sources
- Scale based on multiple conditions
- Use trigger selection logic

**4. Monitor Scaling:**
- Watch ScaledObject status
- Monitor HPA created by KEDA
- Check event source metrics

**5. Test Scaling:**
- Test scale-up scenarios
- Test scale-down scenarios
- Verify event source connectivity

**6. Handle Scale-to-Zero:**
- Ensure applications handle cold starts
- Consider startup time
- Use appropriate minReplicaCount

### KEDA vs HPA

| Feature | KEDA | HPA |
|---------|------|-----|
| **Scaling Based On** | External events | CPU/Memory metrics |
| **Event Sources** | Many (queues, DBs, cloud) | Limited (metrics) |
| **Scale to Zero** | Yes (default) | No (min: 1) |
| **Complexity** | Higher (needs scalers) | Lower (built-in) |
| **Use Case** | Event-driven workloads | Resource-based scaling |

### Key Takeaways

1. **KEDA scales based on events** - External event sources
2. **Many supported scalers** - Queues, databases, cloud services
3. **Scale to zero** - Can scale down to 0 Pods
4. **Works with HPA** - Uses HPA under the hood
5. **Event-driven workloads** - Perfect for queue workers, processors
6. **Requires KEDA installation** - Not standard Kubernetes
7. **Flexible configuration** - Multiple triggers and scalers

---

## Cluster Autoscaler

**Cluster Autoscaler** automatically adjusts the size of the Kubernetes cluster by adding or removing nodes based on resource demands. It ensures there are enough nodes to schedule Pods while removing unused nodes to save costs.

### What is Cluster Autoscaler?

**Simple Explanation:**
Cluster Autoscaler automatically adds nodes to your cluster when Pods can't be scheduled (due to insufficient resources) and removes nodes when they're underutilized. It's like automatically adding or removing servers based on demand.

**Simple Analogy:**
Think of Cluster Autoscaler as an **automatic infrastructure manager**:
- Monitors if Pods can be scheduled
- Adds more servers (nodes) when needed
- Removes unused servers to save money
- Like automatically expanding or shrinking a parking lot

**Key Difference:**
- **HPA/VPA:** Scale Pods within existing nodes
- **Cluster Autoscaler:** Scale the cluster itself (add/remove nodes)

### Why Do We Need Cluster Autoscaler?

**The Problem:**
- Pods can't be scheduled if nodes are full
- Manual node management is time-consuming
- Over-provisioning wastes money
- Under-provisioning causes scheduling failures

**The Solution:**
- Cluster Autoscaler adds nodes automatically
- Removes unused nodes to save costs
- Responds to scheduling demands
- Optimizes cluster size

### How Cluster Autoscaler Works

**Cluster Autoscaler Workflow:**
```
1. Pod can't be scheduled (pending)
   ↓
2. Cluster Autoscaler detects pending Pod
   ↓
3. Calculates if adding node would help
   ↓
4. Adds new node to cluster
   ↓
5. Pod gets scheduled on new node
   ↓
6. Monitors node utilization
   ↓
7. Removes underutilized nodes
```

**Scale Up Conditions:**
- Pods are pending (can't be scheduled)
- Adding a node would allow scheduling
- Node group has capacity

**Scale Down Conditions:**
- Node utilization is below threshold
- All Pods can be moved to other nodes
- Node is not needed for cluster stability

### Cluster Autoscaler Architecture Diagram

```mermaid
graph TB
    PENDING[Pending Pods] --> CA[CA Controller]
    SCHED[Scheduler] --> MON[Monitor]
    MON --> CA
    CA --> DEC[Decision Engine]
    DEC --> NG[Node Group]
    NG --> NEW[New Node]
    DEC --> OLD[Old Node]
    NEW --> NODE1[Node 1]
    OLD --> NODE2[Node 2]
```

### Cluster Autoscaler Configuration

**Deployment YAML (Example for AWS):**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: k8s.gcr.io/autoscaling/cluster-autoscaler:v1.27.0
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=4
        - --stderrthreshold=info
        - --cloud-provider=aws
        - --skip-nodes-with-local-storage=false
        - --expander=least-waste
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/<cluster-name>
        - --balance-similar-node-groups
        - --scale-down-enabled=true
        - --scale-down-delay-after-add=10m
        - --scale-down-unneeded-time=10m
        - --scale-down-utilization-threshold=0.5
```

**Key Configuration Flags:**

**1. Cloud Provider:**
- `--cloud-provider=aws` (AWS)
- `--cloud-provider=gce` (GCP)
- `--cloud-provider=azure` (Azure)

**2. Node Group Discovery:**
- `--node-group-auto-discovery` - Auto-discover node groups
- Or specify node groups manually

**3. Scaling Behavior:**
- `--scale-down-enabled` - Enable scale down
- `--scale-down-delay-after-add` - Wait time after scale up
- `--scale-down-unneeded-time` - Time before removing node
- `--scale-down-utilization-threshold` - Utilization threshold

**4. Expander:**
- `--expander=least-waste` - Minimize waste
- `--expander=most-pods` - Maximize Pods
- `--expander=priority` - Use priority

### Cluster Autoscaler Requirements

**1. Cloud Provider Support:**
- AWS (EKS)
- GCP (GKE)
- Azure (AKS)
- On-premises (limited support)

**2. Node Groups:**
- Auto Scaling Groups (AWS)
- Instance Groups (GCP)
- VM Scale Sets (Azure)

**3. Permissions:**
- IAM roles with node management permissions
- Service account with proper roles

**4. Pod Disruption Budgets:**
- Set PDBs to control scale down
- Prevents removing nodes with critical Pods

### Cluster Autoscaler Best Practices

**1. Set Appropriate Thresholds:**
- Configure scale-down utilization threshold
- Balance between cost and availability
- Consider node startup time

**2. Use Pod Disruption Budgets:**
- Set PDBs for important workloads
- Prevents unwanted scale down
- Ensures availability

**3. Configure Scale Down Delays:**
- Set appropriate delays after scale up
- Prevents rapid scale up/down cycles
- Allows Pods to stabilize

**4. Label Node Groups:**
- Properly label node groups
- Enable auto-discovery
- Organize by workload type

**5. Monitor Cluster Autoscaler:**
- Watch autoscaler logs
- Monitor node additions/removals
- Check for scaling events

**6. Test Scaling:**
- Test scale-up scenarios
- Test scale-down scenarios
- Verify node group configuration

### Cluster Autoscaler Limitations

**1. Cloud Provider Required:**
- Needs cloud provider integration
- Limited on-premises support
- Requires proper IAM roles

**2. Node Startup Time:**
- Nodes take time to join cluster
- May cause temporary scheduling delays
- Consider in capacity planning

**3. Scale Down Constraints:**
- Can't remove nodes with local storage Pods
- Respects Pod Disruption Budgets
- May not scale down if Pods can't be moved

**4. Cost Considerations:**
- Adding nodes increases costs
- Monitor cluster size
- Set appropriate limits

### Key Takeaways

1. **Cluster Autoscaler scales nodes** - Adds/removes cluster nodes
2. **Cloud provider required** - Needs cloud integration
3. **Responds to scheduling** - Adds nodes when Pods pending
4. **Removes unused nodes** - Saves costs
5. **Respects PDBs** - Won't violate Pod Disruption Budgets
6. **Configurable thresholds** - Control scale up/down behavior
7. **Monitor and test** - Ensure proper configuration

---

## Karpenter

**Karpenter** is a high-performance, open-source cluster autoscaler for Kubernetes. It provides faster, more efficient node provisioning compared to Cluster Autoscaler by provisioning exactly the right compute resources for each workload.

### What is Karpenter?

**Simple Explanation:**
Karpenter automatically provisions the right nodes for your workloads. Unlike Cluster Autoscaler which works with node groups, Karpenter can provision any node type that matches your workload requirements, leading to better resource utilization and faster scaling.

**Simple Analogy:**
Think of Karpenter as a **smart, flexible infrastructure manager**:
- Cluster Autoscaler = Fixed parking lot sizes (node groups)
- Karpenter = Dynamic parking spaces (provisions exactly what's needed)
- Faster provisioning
- Better resource fit

**Key Differences from Cluster Autoscaler:**
- **Cluster Autoscaler:** Works with predefined node groups
- **Karpenter:** Provisions any node type that fits
- **Faster:** Sub-minute node provisioning
- **Smarter:** Better bin-packing and resource utilization

### Why Do We Need Karpenter?

**The Problem with Cluster Autoscaler:**
- Works with fixed node groups
- Limited node type selection
- Slower provisioning (minutes)
- Less efficient resource utilization
- Can't mix instance types easily

**The Solution - Karpenter:**
- Provisions any suitable node type
- Faster provisioning (seconds)
- Better resource utilization
- Intelligent bin-packing
- Cost optimization

### How Karpenter Works

**Karpenter Workflow:**
```
1. Pod can't be scheduled (pending)
   ↓
2. Karpenter analyzes Pod requirements
   ↓
3. Finds best node type(s) for the workload
   ↓
4. Provisions node(s) directly (no node groups)
   ↓
5. Pod gets scheduled on new node
   ↓
6. Monitors node utilization
   ↓
7. Consolidates/removes underutilized nodes
```

**Key Features:**
- **Direct Provisioning:** Provisions nodes directly, not through node groups
- **Multi-Instance Support:** Can mix instance types
- **Fast Provisioning:** Sub-minute node provisioning
- **Consolidation:** Automatically consolidates workloads for efficiency
- **Cost Optimization:** Chooses cost-effective instance types

### Karpenter Architecture Diagram

```mermaid
graph TB
    subgraph "Kubernetes Cluster"
        Scheduler[Scheduler<br/>Pod Scheduling]
        Pending[Pending Pods<br/>Can't Schedule]
    end
    
    subgraph "Karpenter Components"
        KarpenterCtrl[Karpenter Controller<br/>Main Controller]
        Analyzer[Workload Analyzer<br/>Requirements]
        Provisioner[Provisioner<br/>Node Provisioning]
        Consolidator[Consolidator<br/>Node Optimization]
    end
    
    subgraph "Cloud Provider API"
        EC2[EC2 API<br/>AWS]
        GCE[GCE API<br/>GCP]
        Azure[Azure API<br/>Azure]
    end
    
    subgraph "Provisioned Nodes"
        Node1[Node 1<br/>Instance Type A]
        Node2[Node 2<br/>Instance Type B]
        Node3[Node 3<br/>Instance Type C]
    end
    
    Pending --> KarpenterCtrl
    Scheduler --> KarpenterCtrl
    KarpenterCtrl --> Analyzer
    Analyzer --> Provisioner
    Provisioner --> EC2
    Provisioner --> GCE
    Provisioner --> Azure
    EC2 --> Node1
    GCE --> Node2
    Azure --> Node3
    KarpenterCtrl --> Consolidator
    Consolidator --> Node1
    
    style KarpenterCtrl fill:#326ce5,color:#fff
    style Provisioner fill:#00d4aa,color:#fff
    style Node1 fill:#f4a261,color:#000
```

### Karpenter Provisioner

**Provisioner** is a Karpenter CRD that defines how nodes should be provisioned.

**Basic Provisioner YAML:**
```yaml
apiVersion: karpenter.sh/v1beta1
kind: NodePool
metadata:
  name: default
spec:
  template:
    metadata:
      labels:
        intent: apps
    spec:
      nodeClassRef:
        name: default
      requirements:
        - key: kubernetes.io/arch
          operator: In
          values: ["amd64"]
        - key: kubernetes.io/os
          operator: In
          values: ["linux"]
        - key: karpenter.sh/capacity-type
          operator: In
          values: ["spot", "on-demand"]
        - key: node.kubernetes.io/instance-type
          operator: In
          values: ["m5.large", "m5.xlarge", "m5.2xlarge"]
  limits:
    cpu: "1000"
    memory: 1000Gi
  disruption:
    consolidationPolicy: WhenEmpty
    consolidateAfter: 30s
```

**Key Fields Explained:**

**1. template:**
- **metadata.labels:** Labels applied to provisioned nodes
- **spec.nodeClassRef:** Reference to EC2NodeClass (AWS) or similar
- **spec.requirements:** Node selection requirements

**2. requirements:**
- **key:** Node selector key (arch, os, instance-type, etc.)
- **operator:** In, NotIn, Exists, etc.
- **values:** Allowed values

**3. limits:**
- **cpu:** Maximum CPU across all nodes
- **memory:** Maximum memory across all nodes

**4. disruption:**
- **consolidationPolicy:** WhenEmpty, WhenUnderutilized
- **consolidateAfter:** Time before consolidation

### EC2NodeClass (AWS)

**EC2NodeClass** defines EC2-specific configuration for Karpenter.

**Example EC2NodeClass:**
```yaml
apiVersion: karpenter.k8s.aws/v1beta1
kind: EC2NodeClass
metadata:
  name: default
spec:
  amiFamily: AL2
  role: KarpenterNodeRole
  subnetSelectorTerms:
    - tags:
        karpenter.sh/discovery: <cluster-name>
  securityGroupSelectorTerms:
    - tags:
        karpenter.sh/discovery: <cluster-name>
  tags:
    Environment: production
    ManagedBy: Karpenter
```

**Key Fields:**
- **amiFamily:** AMI family (AL2, Bottlerocket, Ubuntu)
- **role:** IAM role for nodes
- **subnetSelectorTerms:** Subnet selection
- **securityGroupSelectorTerms:** Security group selection
- **tags:** Tags applied to nodes

### Karpenter Installation

**Prerequisites:**
- Kubernetes cluster (EKS, GKE, AKS, or self-managed)
- IAM roles and permissions (for cloud provider)
- Node requirements configured

**Installation Steps (AWS EKS):**
```bash
# 1. Create IAM roles and policies
# 2. Install Karpenter
helm repo add karpenter oci://public.ecr.aws/karpenter
helm repo update
helm upgrade --install karpenter oci://public.ecr.aws/karpenter/karpenter \
  --namespace karpenter \
  --create-namespace \
  --set serviceAccount.annotations."eks\.amazonaws\.com/role-arn"=<KarpenterRoleArn> \
  --set settings.clusterName=<cluster-name> \
  --set settings.defaultInstanceProfile=<InstanceProfile> \
  --wait

# 3. Create NodePool
kubectl apply -f nodepool.yaml

# 4. Create EC2NodeClass (AWS)
kubectl apply -f ec2nodeclass.yaml
```

### Karpenter vs Cluster Autoscaler

| Feature | Cluster Autoscaler | Karpenter |
|---------|-------------------|-----------|
| **Node Groups** | Required (predefined) | Not required |
| **Node Types** | Limited to node group types | Any suitable type |
| **Provisioning Speed** | Minutes | Seconds (sub-minute) |
| **Resource Utilization** | Good | Excellent (bin-packing) |
| **Instance Mixing** | Limited | Full support |
| **Consolidation** | Basic | Advanced |
| **Cost Optimization** | Good | Better |
| **Complexity** | Lower | Higher |
| **Maturity** | Very mature | Newer (growing) |

### Karpenter Best Practices

**1. Configure Appropriate Limits:**
- Set CPU and memory limits
- Prevent runaway provisioning
- Control costs

**2. Use Spot Instances:**
- Configure spot instance support
- Significant cost savings
- Handle interruptions gracefully

**3. Set Consolidation Policy:**
- Use WhenUnderutilized for cost savings
- Use WhenEmpty for faster consolidation
- Balance between cost and stability

**4. Configure Requirements:**
- Specify instance types
- Set capacity type preferences
- Define architecture requirements

**5. Use Multiple NodePools:**
- Different NodePools for different workloads
- GPU nodes, compute-optimized, etc.
- Workload-specific optimization

**6. Monitor Karpenter:**
- Watch provisioning events
- Monitor consolidation
- Check node utilization
- Review costs

**7. Test Provisioning:**
- Test scale-up scenarios
- Test consolidation
- Verify instance selection

### Karpenter Limitations

**1. Cloud Provider Support:**
- Best support for AWS (EKS)
- GCP and Azure support available
- On-premises support limited

**2. Learning Curve:**
- Newer technology
- Different concepts than Cluster Autoscaler
- Requires understanding of Provisioners

**3. Stateful Workloads:**
- Consolidation can disrupt workloads
- Configure disruption budgets
- Use appropriate consolidation policies

**4. Cost Management:**
- Can provision many nodes quickly
- Set appropriate limits
- Monitor provisioning closely

### Key Takeaways

1. **Karpenter is a modern autoscaler** - Faster and more efficient than Cluster Autoscaler
2. **Direct node provisioning** - No node groups required
3. **Fast provisioning** - Sub-minute node provisioning
4. **Better resource utilization** - Intelligent bin-packing
5. **Cost optimization** - Chooses cost-effective instances
6. **Consolidation** - Automatically optimizes node usage
7. **Requires configuration** - NodePools and node classes needed
8. **Best for AWS** - Excellent EKS support

---

### Autoscaling Comparison

| Feature | HPA | VPA | KEDA | Cluster Autoscaler | Karpenter |
|---------|-----|-----|------|-------------------|-----------|
| **What It Scales** | Pods | Resources | Pods | Nodes | Nodes |
| **Based On** | CPU/Memory metrics | Historical usage | External events | Scheduling needs | Scheduling needs |
| **Scale Direction** | Horizontal | Vertical | Horizontal | Infrastructure | Infrastructure |
| **Scale to Zero** | No (min: 1) | N/A | Yes | No | No |
| **Use Case** | Web services | Resource optimization | Event-driven | Cluster capacity | Cluster capacity |
| **Complexity** | Low | Medium | High | High | High |
| **Standard K8s** | Yes | No | No | No | No |
| **Node Groups** | N/A | N/A | N/A | Required | Not required |
| **Provisioning Speed** | N/A | N/A | N/A | Minutes | Seconds |
| **Resource Fit** | N/A | N/A | N/A | Good | Excellent |

### Combining Autoscalers

**Common Combinations:**
1. **HPA + Cluster Autoscaler/Karpenter:**
   - HPA scales Pods
   - Cluster Autoscaler/Karpenter adds nodes when needed
   - Most common combination

2. **KEDA + Cluster Autoscaler/Karpenter:**
   - KEDA scales based on events
   - Cluster Autoscaler/Karpenter adds nodes for event processing
   - For event-driven workloads

3. **VPA (Initial) + HPA:**
   - VPA optimizes resources
   - HPA scales Pod count
   - Use different metrics

4. **HPA + Karpenter:**
   - HPA scales Pods horizontally
   - Karpenter provisions optimal nodes
   - Best for AWS EKS with fast scaling needs

**Don't Combine:**
- VPA (Auto) + HPA (CPU/Memory) - Can conflict
- Multiple HPAs on same resource - Use one HPA
- Cluster Autoscaler + Karpenter - Choose one node autoscaler

### Key Takeaways

1. **Autoscaling optimizes resources** - Automatically adjusts to demand
2. **Multiple autoscalers available** - Choose based on needs
3. **HPA for horizontal scaling** - Most common for Pod scaling
4. **VPA for resource optimization** - Adjusts CPU/memory per Pod
5. **KEDA for event-driven scaling** - Based on external events
6. **Cluster Autoscaler for infrastructure** - Traditional node autoscaler
7. **Karpenter for modern infrastructure** - Fast, efficient node autoscaler
8. **Can combine autoscalers** - Use multiple together carefully
9. **Monitor and test** - Ensure autoscaling works as expected

Autoscaling is essential for running efficient, cost-effective Kubernetes clusters. Choose the right autoscaler(s) based on your workload characteristics and requirements.

</details>

---

<details>
<summary><h2>Taints and Tolerations</h2></summary>

## Taints and Tolerations

**Taints and Tolerations** are mechanisms in Kubernetes that allow you to mark nodes (taints) and allow pods to be scheduled on those nodes (tolerations). They work together to control which pods can be scheduled on which nodes.

### What are Taints and Tolerations?

**Simple Explanation:**
- **Taint** = A "no entry" sign on a node - prevents pods from being scheduled
- **Toleration** = A "permission slip" for a pod - allows it to ignore the taint

**Analogy:**
Think of a parking lot:
- **Taint** = "Reserved for VIPs only" sign (regular cars can't park)
- **Toleration** = VIP pass (allows you to park in reserved spots)
- **Regular pods** = Regular cars (can't park in reserved spots)
- **Pods with toleration** = VIP cars (can park in reserved spots)

### Taints and Tolerations Diagram

```mermaid
graph TB
    Node1[Node 1<br/>No Taint]
    Node2[Node 2<br/>Taint: dedicated=gpu]
    Node3[Node 3<br/>Taint: dedicated=gpu]
    
    Pod1[Pod 1<br/>No Toleration] -->|Can Schedule| Node1
    Pod1 -.->|Cannot Schedule| Node2
    Pod1 -.->|Cannot Schedule| Node3
    
    Pod2[Pod 2<br/>Toleration: dedicated=gpu] -->|Can Schedule| Node1
    Pod2 -->|Can Schedule| Node2
    Pod2 -->|Can Schedule| Node3
    
    style Node1 fill:#90ee90,color:#000
    style Node2 fill:#ff9800,color:#fff
    style Node3 fill:#ff9800,color:#fff
    style Pod1 fill:#f4a261,color:#000
    style Pod2 fill:#4fc3f7,color:#000
```

### Taints

**Taint** is a property of a node that repels pods from being scheduled on it, unless the pod has a matching toleration.

#### Taint Structure

A taint has three parts:
- **Key:** Identifier for the taint
- **Value:** Optional value for the taint
- **Effect:** What happens when pod doesn't have toleration
  - **NoSchedule:** Pod won't be scheduled (hard constraint)
  - **PreferNoSchedule:** Try not to schedule (soft constraint)
  - **NoExecute:** Evict existing pods without toleration

#### Taint Effects Explained

**NoSchedule:**
- Pods without matching toleration **cannot be scheduled**
- Existing pods without toleration **remain running**
- Hard constraint - scheduler won't place pods

**PreferNoSchedule:**
- Scheduler **tries to avoid** scheduling pods without toleration
- Will schedule if no other nodes available
- Soft constraint - preference, not requirement

**NoExecute:**
- Pods without matching toleration **cannot be scheduled**
- Existing pods without toleration **are evicted**
- Most restrictive - affects existing pods

#### Adding Taints to Nodes

```bash
# Add taint to node
kubectl taint nodes <node-name> key=value:effect

# Examples
kubectl taint nodes node1 dedicated=gpu:NoSchedule
kubectl taint nodes node1 app=special:PreferNoSchedule
kubectl taint nodes node1 maintenance=true:NoExecute
```

#### Viewing Taints

```bash
# View node taints
kubectl describe node <node-name>

# Get taints in YAML
kubectl get node <node-name> -o yaml | grep taint
```

#### Removing Taints

```bash
# Remove taint from node
kubectl taint nodes <node-name> key=value:effect-

# Remove all taints with key
kubectl taint nodes <node-name> key-

# Example
kubectl taint nodes node1 dedicated=gpu:NoSchedule-
```

### Tolerations

**Toleration** is a property of a pod that allows it to be scheduled on a node with a matching taint.

#### Toleration Structure

```yaml
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "gpu"
  effect: "NoSchedule"
```

#### Toleration Fields Explained

**key:**
- Taint key to match (required)
- Example: `"dedicated"`, `"app"`

**operator:**
- **Equal:** Key and value must match exactly
- **Exists:** Key must exist (value ignored)

**value:**
- Taint value to match (required if operator is Equal)
- Ignored if operator is Exists

**effect:**
- Taint effect to match (optional)
- **NoSchedule**, **PreferNoSchedule**, **NoExecute**
- If not specified, matches all effects

**tolerationSeconds:**
- For NoExecute effect only
- How long to wait before evicting pod
- Only applies to NoExecute taints

#### Adding Tolerations to Pods

**Method 1: In Pod Spec**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
  containers:
  - name: app
    image: nginx:latest
```

**Method 2: In Deployment**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gpu-deployment
spec:
  replicas: 3
  template:
    spec:
      tolerations:
      - key: "dedicated"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
      containers:
      - name: app
        image: nginx:latest
```

#### Common Toleration Patterns

**Pattern 1: Match Specific Taint**

```yaml
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "gpu"
  effect: "NoSchedule"
```

**Pattern 2: Match Any Value for Key**

```yaml
tolerations:
- key: "dedicated"
  operator: "Exists"
  effect: "NoSchedule"
```

**Pattern 3: Match Any Effect**

```yaml
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "gpu"
  # No effect specified - matches all effects
```

**Pattern 4: Tolerate All Taints (Master Node)**

```yaml
tolerations:
- key: "node-role.kubernetes.io/master"
  operator: "Exists"
  effect: "NoSchedule"
- key: "node-role.kubernetes.io/control-plane"
  operator: "Exists"
  effect: "NoSchedule"
```

### Taints and Tolerations Use Cases

**1. Dedicated Nodes**

```bash
# Taint node for GPU workloads
kubectl taint nodes gpu-node-1 dedicated=gpu:NoSchedule

# Pod with toleration can use GPU node
```

**2. Master/Control Plane Nodes**

```bash
# Master nodes are tainted by default
# System pods have tolerations
# User pods cannot schedule on master
```

**3. Maintenance Mode**

```bash
# Taint node for maintenance
kubectl taint nodes node1 maintenance=true:NoExecute

# Existing pods are evicted
# New pods won't schedule
```

**4. Specialized Workloads**

```bash
# Taint nodes for specific workloads
kubectl taint nodes node1 app=special:PreferNoSchedule

# Prefer not to schedule regular pods
# But will if needed
```

### Taints and Tolerations Examples

#### Example 1: GPU Node

**Taint the Node:**
```bash
kubectl taint nodes gpu-node-1 dedicated=gpu:NoSchedule
```

**Pod with Toleration:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
  nodeSelector:
    accelerator: nvidia-tesla-k80
  containers:
  - name: gpu-app
    image: nvidia/cuda:latest
```

#### Example 2: Master Node Pod

**Master nodes are automatically tainted:**
```bash
# Master node has taint
node-role.kubernetes.io/master:NoSchedule
```

**System Pod with Toleration:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: system-pod
spec:
  tolerations:
  - key: "node-role.kubernetes.io/master"
    operator: "Exists"
    effect: "NoSchedule"
  containers:
  - name: system-app
    image: system-image:latest
```

#### Example 3: Maintenance Mode

**Taint for Maintenance:**
```bash
kubectl taint nodes node1 maintenance=true:NoExecute
```

**Pod with Temporary Toleration:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: maintenance-pod
spec:
  tolerations:
  - key: "maintenance"
    operator: "Equal"
    value: "true"
    effect: "NoExecute"
    tolerationSeconds: 3600  # Wait 1 hour before eviction
  containers:
  - name: app
    image: nginx:latest
```

### Taints vs Node Selectors

| Feature | Taints | Node Selectors |
|---------|--------|----------------|
| **Purpose** | Repel pods | Attract pods |
| **Direction** | Node → Pod | Pod → Node |
| **Default Behavior** | Pods can't schedule | Pods can schedule anywhere |
| **Use Case** | Dedicated nodes, maintenance | Specific hardware, zones |

**They Work Together:**
- Use **taints** to repel pods from nodes
- Use **tolerations** to allow specific pods
- Use **node selectors** to attract pods to nodes

### Managing Taints and Tolerations

**Node Taints:**
```bash
# Add taint
kubectl taint nodes <node> key=value:effect

# Remove taint
kubectl taint nodes <node> key=value:effect-

# List node taints
kubectl describe node <node>
```

**Pod Tolerations:**
```bash
# View pod tolerations
kubectl get pod <pod-name> -o yaml | grep -A 5 tolerations

# View deployment tolerations
kubectl get deployment <deployment-name> -o yaml | grep -A 5 tolerations
```

### Taints and Tolerations Best Practices

1. **Use for dedicated nodes** - GPU nodes, specialized hardware
2. **Master node taints** - System pods need tolerations
3. **Maintenance mode** - Use NoExecute to drain nodes
4. **Combine with node selectors** - For precise scheduling
5. **Document taints** - Keep track of node taints
6. **Use meaningful keys** - Clear, descriptive taint keys
7. **Test tolerations** - Verify pods can schedule correctly
8. **Monitor scheduling** - Check if pods are being blocked

### Common Taint Patterns

**Pattern 1: Dedicated GPU Nodes**
```bash
kubectl taint nodes gpu-node-1 dedicated=gpu:NoSchedule
```

**Pattern 2: Maintenance Mode**
```bash
kubectl taint nodes node1 maintenance=true:NoExecute
```

**Pattern 3: Special Workloads**
```bash
kubectl taint nodes node1 workload=special:PreferNoSchedule
```

**Pattern 4: Zone-Based**
```bash
kubectl taint nodes node1 zone=production:NoSchedule
```

### Troubleshooting

**Pod Not Scheduling:**
```bash
# Check node taints
kubectl describe node <node-name>

# Check pod tolerations
kubectl get pod <pod-name> -o yaml | grep tolerations

# Check events
kubectl describe pod <pod-name>
```

**Pod Evicted:**
```bash
# Check for NoExecute taints
kubectl describe node <node-name>

# Check pod tolerations
kubectl get pod <pod-name> -o yaml
```

### Key Takeaways

1. **Taints repel pods** - Prevent scheduling on nodes
2. **Tolerations allow pods** - Override taints
3. **Three effects** - NoSchedule, PreferNoSchedule, NoExecute
4. **Use for dedicated nodes** - GPU, specialized hardware
5. **Master nodes are tainted** - System pods need tolerations
6. **Combine with selectors** - For precise scheduling
7. **NoExecute evicts pods** - Removes existing pods
8. **Document your taints** - Keep track of node configurations

Taints and Tolerations are essential for controlling pod placement and ensuring specialized workloads run on appropriate nodes.

</details>
