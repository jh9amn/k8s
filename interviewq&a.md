- [1) What is the Difference Between Docker and Kubernetes?](#1-what-is-the-difference-between-docker-and-kubernetes)
- [2) What are the main component of kubernetes architecture ?](#2-What-are-the-main-component-of-kubernetes-architecture)

# 1) What is the Difference Between Docker and Kubernetes?
Docker and Kubernetes are often used together, but **they solve different problems**.

* **Docker** is a **containerization platform**. It packages your application and its dependencies into a container.
* **Kubernetes (K8s)** is a **container orchestration platform**. It manages, scales, and monitors containers across multiple machines.

Think of it like this:

> **Docker = Creates and runs containers**
>
> **Kubernetes = Manages thousands of containers efficiently**

---

# Real-Life Analogy

Imagine you own a food delivery business.

### 🥡 Docker = Food Box

Docker packages the food along with everything needed.

* Food (Application Code)
* Spoon/Fork (Dependencies)
* Napkins (Libraries)
* Box (Container)

Every customer gets exactly the same package.

```
+---------------------------+
|        Docker Image       |
|---------------------------|
| App Code                  |
| Node/Python/Java Runtime  |
| Libraries                 |
| Config                    |
+---------------------------+

          |
          v

+---------------------------+
|     Running Container     |
+---------------------------+
```

Docker's responsibility ends here.

---

### 🚚 Kubernetes = Delivery Manager

Now imagine you have **10,000 food boxes**.

Questions arise:

* Which driver should deliver which order?
* What if one driver becomes sick?
* How many drivers are needed during peak hours?
* Which warehouse has free space?

Docker **cannot** solve these.

Kubernetes acts like a delivery manager.

It decides:

* Where containers run
* When to create new containers
* When to delete containers
* How traffic reaches them
* What happens if a machine dies

---

# Responsibilities Comparison

| Docker                      | Kubernetes                |
| --------------------------- | ------------------------- |
| Builds container images     | Deploys containers        |
| Runs containers             | Manages containers        |
| Packages applications       | Orchestrates applications |
| Single host focused         | Multi-node cluster        |
| Manual scaling              | Automatic scaling         |
| Doesn't self-heal           | Self-healing              |
| No load balancing           | Built-in load balancing   |
| No rolling updates          | Rolling updates           |
| Doesn't schedule containers | Intelligent scheduling    |

---

# Example

Suppose you have a React + Node application.

Without Kubernetes:

```
docker run backend
docker run frontend
docker run redis
docker run mongodb
```

Everything is manual.

If backend crashes:

❌ You restart it manually.

---

With Kubernetes:

```
Deployment
    |
    +---- Pod 1
    +---- Pod 2
    +---- Pod 3
```

If Pod 2 crashes:

```
Pod 2 ❌

↓

ReplicaSet notices

↓

Creates new Pod 2

↓

Application keeps running
```

This is **self-healing**.

---

# Scaling Example

Suppose 10 users visit your application.

One container is enough.

```
Backend

Users
 |
 v
+---------+
|Container|
+---------+
```

Now 10,000 users arrive.

Docker alone:

You manually run more containers.

```
docker run backend
docker run backend
docker run backend
docker run backend
```

You must also manually configure load balancing.

---

With Kubernetes:

```
Users
   |
   v
 Service
   |
-----------------------------
|      |      |      |      |
Pod1  Pod2   Pod3   Pod4   Pod5
```

You simply change:

```yaml
replicas: 5
```

or enable:

```yaml
HorizontalPodAutoscaler
```

Kubernetes automatically creates more Pods.

---

# Self-Healing

Docker:

```
Container crashes

↓

Stopped

↓

Human restarts it
```

Kubernetes:

```
Pod crashes

↓

Controller notices

↓

Creates new Pod

↓

Traffic continues
```

---

# Load Balancing

Docker:

```
User
 |
 v

Which container should receive request?

❌ Docker doesn't know.
```

Kubernetes:

```
User
  |
  v
Service
 / | \
P1 P2 P3
```

The **Service** distributes traffic across healthy Pods.

---

# Rolling Updates

Suppose your app is version **1.0**.

You deploy **2.0**.

Docker:

```
Stop old

↓

Start new

↓

Downtime
```

Kubernetes:

```
v1  v1  v1

↓

v2  v1  v1

↓

v2  v2  v1

↓

v2  v2  v2
```

Users experience little to no downtime.

---

# Architecture

```
                 Kubernetes Cluster

              +----------------------+
              |   Control Plane      |
              +----------------------+
                       |
        ---------------------------------------
        |                 |                   |
   Worker Node 1     Worker Node 2      Worker Node 3
        |                 |                   |
   +------------+    +------------+    +------------+
   | Pod        |    | Pod        |    | Pod        |
   | Docker/CRI |    | Docker/CRI |    | Docker/CRI |
   +------------+    +------------+    +------------+
```

Docker (or another container runtime) runs the containers, while Kubernetes manages them.

> **Note:** Modern Kubernetes no longer uses Docker directly as its runtime. Instead, it uses a **Container Runtime Interface (CRI)**-compatible runtime such as **containerd** or **CRI-O**. Docker itself uses **containerd** internally.

---

# Workflow

```
Write Code
     |
     v
Create Dockerfile
     |
     v
Build Docker Image
     |
     v
Push Image to Registry
     |
     v
Kubernetes pulls Image
     |
     v
Creates Pods
     |
     v
Exposes via Service
     |
     v
Users Access Application
```

---

# Can Docker Work Without Kubernetes?

✅ Yes.

For small applications:

```
Laptop

Docker

Container
```

No Kubernetes needed.

Examples:

* Local development
* Personal projects
* Small websites
* Testing

---

# Can Kubernetes Work Without Docker?

✅ Yes.

Modern Kubernetes commonly uses:

* **containerd**
* **CRI-O**

Docker is **not required** as the runtime.

---

# When to Use What?

| Scenario              | Docker                  | Kubernetes            |
| --------------------- | ----------------------- | --------------------- |
| Local development     | ✅                       | ❌ Usually unnecessary |
| Learning containers   | ✅                       | ❌                     |
| Small project         | ✅                       | ❌                     |
| Microservices         | ✅                       | ✅                     |
| Production deployment | ✅ (for building images) | ✅                     |
| Auto scaling          | ❌                       | ✅                     |
| Self-healing          | ❌                       | ✅                     |
| Load balancing        | ❌                       | ✅                     |
| High availability     | ❌                       | ✅                     |

---

# Key Differences at a Glance

| Feature         | Docker                          | Kubernetes                      |
| --------------- | ------------------------------- | ------------------------------- |
| Primary Purpose | Build and run containers        | Orchestrate containers          |
| Scope           | Single host                     | Multiple hosts (cluster)        |
| Scaling         | Manual                          | Automatic                       |
| Self-healing    | No                              | Yes                             |
| Load Balancing  | No                              | Yes                             |
| Rolling Updates | Basic/manual                    | Built-in                        |
| Scheduling      | No                              | Intelligent                     |
| Best For        | Development, packaging, testing | Production, distributed systems |

## Summary

* **Docker** packages your application into a portable container and runs it consistently across environments.
* **Kubernetes** manages large numbers of containers, ensuring they stay healthy, scale with demand, recover from failures, and are accessible to users.
* In a typical production workflow, you **build the application with Docker (or another image builder)**, store the image in a registry, and **use Kubernetes to deploy and manage those containers** across a cluster.
  
----

# What are the main component of kubernetes architecture ?

The Kubernetes architecture is divided into **two main parts**:

1. **Control Plane (Master Node)** – Makes decisions and manages the cluster.
2. **Worker Nodes (Data Plane)** – Runs your applications inside Pods.

---

# Kubernetes Architecture

```text
                   Kubernetes Cluster
   -------------------------------------------------

               Control Plane (Master Node)
   +------------------------------------------------+
   | API Server                                     |
   | Scheduler                                      |
   | Controller Manager                             |
   | etcd                                           |
   | Cloud Controller Manager (Optional)            |
   +------------------------------------------------+
                      |
      -----------------------------------------
      |                  |                    |
+-------------+   +-------------+    +-------------+
| Worker Node |   | Worker Node |    | Worker Node |
+-------------+   +-------------+    +-------------+
| Kubelet     |   | Kubelet     |    | Kubelet     |
| Kube Proxy  |   | Kube Proxy  |    | Kube Proxy  |
| Container   |   | Container   |    | Container   |
| Runtime     |   | Runtime     |    | Runtime     |
| Pods        |   | Pods        |    | Pods        |
+-------------+   +-------------+    +-------------+
```

---

# 1. Control Plane (Master Node)

The **Control Plane** is the **brain of Kubernetes**. It manages the entire cluster and decides where applications should run.

### Components

## A. API Server

The **API Server** is the entry point of Kubernetes.

### Responsibilities

* Receives all user requests
* Validates requests
* Authenticates users
* Updates the cluster state in `etcd`
* Communicates with every Kubernetes component

### Example

```bash
kubectl apply -f deployment.yaml
```

Flow:

```text
kubectl
   │
   ▼
API Server
   │
   ▼
Other Kubernetes Components
```

Without the API Server, no Kubernetes component communicates directly with users.

---

## B. Scheduler

The **Scheduler** decides **which Worker Node should run a Pod**.

It checks:

* Available CPU
* Available Memory
* Node health
* Labels and taints
* Resource requests

Example:

```text
Pod Created

↓

Scheduler checks nodes

↓

Node 2 has enough resources

↓

Assign Pod to Node 2
```

The scheduler **does not create Pods**; it only chooses the best node.

---

## C. Controller Manager

The **Controller Manager** continuously watches the cluster and ensures the desired state matches the actual state.

Examples of controllers:

* ReplicaSet Controller
* Deployment Controller
* Node Controller
* Job Controller
* Endpoint Controller

Example:

Desired state:

```text
Replicas = 3
```

Current state:

```text
Running Pods = 2
```

Controller Manager detects the difference and creates one more Pod.

---

## D. etcd

`etcd` is Kubernetes' distributed key-value database.

It stores:

* Cluster configuration
* Deployments
* Services
* Secrets
* ConfigMaps
* Nodes
* Pods
* Cluster state

Example:

```text
User creates Deployment

↓

API Server

↓

etcd stores Deployment information
```

If `etcd` is lost and has no backup, the cluster state cannot be recovered.

---

## E. Cloud Controller Manager (Optional)

Used when Kubernetes runs on cloud platforms like:

* AWS
* Azure
* Google Cloud

Responsibilities:

* Create Load Balancers
* Manage cloud disks
* Manage cloud networking
* Manage cloud nodes

It is not required for local clusters like Minikube.

---

# 2. Worker Node (Data Plane)

The **Worker Node** is where your application actually runs.

Each Worker Node contains:

* Kubelet
* Kube Proxy
* Container Runtime
* Pods

---

## A. Kubelet

The **Kubelet** is the agent running on every Worker Node.

Responsibilities:

* Registers the node with the cluster
* Receives Pod instructions from the API Server
* Talks to the container runtime
* Starts containers
* Monitors Pod health
* Reports status back to the Control Plane

Example:

```text
API Server

↓

Kubelet

↓

Container Runtime

↓

Pod Starts
```

---

## B. Kube Proxy

`kube-proxy` handles networking on the Worker Node.

Responsibilities:

* Assigns networking rules
* Enables Pod-to-Pod communication
* Implements Service load balancing
* Forwards traffic to the correct Pods

Example:

```text
User

↓

Service

↓

kube-proxy

↓

Pod 1
Pod 2
Pod 3
```

---

## C. Container Runtime

The **Container Runtime** runs the containers.

Common runtimes:

* containerd
* CRI-O

(Older Kubernetes versions could use Docker through a shim, but modern Kubernetes uses CRI-compatible runtimes directly.)

Responsibilities:

* Pull container images
* Create containers
* Start containers
* Stop containers
* Delete containers

---

## D. Pods

A **Pod** is the smallest deployable unit in Kubernetes.

A Pod can contain:

* One container (most common)
* Multiple tightly coupled containers

Example:

```text
Pod
 ├── Application Container
 └── Sidecar Container (optional)
```

---

# Complete Request Flow

```text
User

↓

kubectl apply

↓

API Server

↓

etcd stores desired state

↓

Scheduler selects Worker Node

↓

Kubelet receives instruction

↓

Container Runtime creates Pod

↓

Pod starts

↓

kube-proxy exposes networking

↓

Application becomes available
```

---

# Component Summary

| Component                    | Purpose                                                   |
| ---------------------------- | --------------------------------------------------------- |
| **API Server**               | Entry point for all Kubernetes requests                   |
| **Scheduler**                | Chooses the best Worker Node for Pods                     |
| **Controller Manager**       | Maintains the desired state of the cluster                |
| **etcd**                     | Stores all cluster data and configuration                 |
| **Cloud Controller Manager** | Integrates Kubernetes with cloud services                 |
| **Kubelet**                  | Runs and monitors Pods on each Worker Node                |
| **Kube Proxy**               | Manages networking and Service load balancing             |
| **Container Runtime**        | Pulls images and runs containers                          |
| **Pod**                      | Smallest deployable unit that runs application containers |

### Easy way to remember

* **Control Plane = Brain** → Decides **what should happen**.
* **Worker Nodes = Muscles** → Actually **run your applications**.
* **API Server = Gatekeeper** → Receives all requests.
* **Scheduler = Planner** → Chooses where Pods should run.
* **Controller Manager = Supervisor** → Ensures the desired state is maintained.
* **etcd = Database** → Stores the cluster's configuration and state.
* **Kubelet = Worker** → Creates and monitors Pods.
* **kube-proxy = Network Manager** → Routes traffic to the correct Pods.
* **Container Runtime = Engine** → Runs the containers inside Pods.

