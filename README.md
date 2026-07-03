# Kubernetes Notes

## 📚 Table of Contents

- [Introduction](#introduction)
- [What is Kubernetes?](#what-is-kubernetes)
- [Kubernetes Architecture](#Kubernetes-Architecture)
- [Minikube](#minikube)
- [What is a Pod in Kubernetes?](#What-is-a-Pod-in-Kubernetes)


## Introduction

kubernattes container orcstation plaform
kubernates by default cluster in nature
Kubernetes is an open-source container orchestration platform used to automate the deployment, scaling, and management of containerized applications.

## What is Kubernetes?

### Problem Solving
- single host nature
- auto scaling
- auto healing
- exterprise level standard support
  - load balancing
  - firewall
  - auto scaling
  - auto healing
  - api gateways
    
---

## Kubernetes Architecture

```text
                           Kubernetes Cluster
---------------------------------------------------------------------------------

                    CONTROL PLANE (Master Node)

      +-----------------------------------------------------------+
      |                      API Server                           |
      |                (Entry Point of Cluster)                   |
      +-----------------------------------------------------------+
                 |              |              |
                 |              |              |
      +----------+----+   +-----+------+   +---+----------------+
      | Scheduler     |   | Controller |   |      etcd          |
      |               |   | Manager    |   | Cluster Database   |
      +---------------+   +------------+   +--------------------+
                                   |
                                   |
                         +-----------------------+
                         | Cloud Controller      |
                         | Manager (CCM)         |
                         +-----------------------+

                               ||
                               ||
===============================||====================================
                               ||
                               \/

                     WORKER NODE (Data Plane)

     +-------------------------------------------------------------+
     |                         kubelet                             |
     |    Communicates with API Server and manages Pods            |
     +-------------------------------------------------------------+

     +-------------------------------------------------------------+
     |                  Container Runtime                          |
     |     (containerd / CRI-O / Docker)                           |
     +-------------------------------------------------------------+

     +-------------------------------------------------------------+
     |                      kube-proxy                             |
     |      Networking, Service Discovery & Load Balancing         |
     +-------------------------------------------------------------+

     +-------------------------------------------------------------+
     |                 Pods (Containers Running)                   |
     +-------------------------------------------------------------+

```

---

### Control Plane Components

The Control Plane manages the entire Kubernetes cluster. It decides **what should run, where it should run, and continuously monitors the cluster state.**

---

#### 1. API Server

### Purpose
- Entry point of Kubernetes.
- Receives every request from users, kubectl, or other components.
- Validates requests.
- Updates the cluster state in **etcd**.

### Responsibilities
- Authentication
- Authorization
- Request validation
- Communication between all Kubernetes components

**Remember:** Every component talks to the API Server, not directly with each other.

---

#### 2. Scheduler

### Purpose

Assigns newly created Pods to the best Worker Node.

### Scheduling Decision Based On

- Available CPU
- Available Memory
- Node Labels
- Taints & Tolerations
- Affinity Rules
- Resource Requests

**Example**

```
Pod Created
      ↓
Scheduler checks available nodes
      ↓
Selects Node-2
      ↓
Pod assigned to Node-2
```

---

#### 3. etcd

### Purpose

Distributed Key-Value Database.

Stores the entire cluster state.

### Stores

- Pods
- Nodes
- Services
- ConfigMaps
- Secrets
- Deployments
- ReplicaSets

If etcd is lost, the cluster loses its state.

---

#### 4. Controller Manager

### Purpose

Runs multiple controllers that continuously compare

> Desired State vs Current State

If they differ, controllers work to make them equal.

### Important Controllers

### ReplicaSet Controller

Ensures the required number of Pod replicas are always running.

Example

Desired Pods = 3

Current Pods = 2

→ Creates 1 new Pod.

---

Other Controllers

- Deployment Controller
- Node Controller
- Namespace Controller
- Job Controller
- Endpoint Controller

---

#### 5. Cloud Controller Manager (CCM)

Used only when Kubernetes runs on Cloud Providers.

Examples

- AWS
- Azure
- Google Cloud (GCP)

### Responsibilities

- Creates Load Balancers
- Creates Cloud Storage Volumes
- Manages Cloud Nodes
- Manages Networking Resources

Without CCM, Kubernetes cannot communicate with cloud provider APIs.

---

### Worker Node Components (Data Plane)

Worker Nodes actually run your applications.

---

#### 1. kubelet

### Purpose

Node Agent.

Runs on every Worker Node.

### Responsibilities

- Receives Pod specifications from API Server.
- Creates Pods using Container Runtime.
- Monitors Pod health.
- Reports status back to API Server.

Think of kubelet as the **manager of a worker node**.

---

#### 2. Container Runtime

Responsible for running containers.

Examples

- containerd
- CRI-O
- Docker (legacy support)

Responsibilities

- Pull images
- Start Containers
- Stop Containers
- Delete Containers

---

#### 3. kube-proxy

Handles networking inside Kubernetes.

### Responsibilities

- Assigns networking rules
- Service discovery
- Load balancing
- Pod communication
- Maintains iptables/IPVS rules

Example

```
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

Traffic is automatically balanced among Pods.

---

### Complete Workflow

```
User
   |
kubectl apply deployment.yaml
   |
API Server
   |
Stored in etcd
   |
Scheduler selects Worker Node
   |
kubelet receives Pod Spec
   |
Container Runtime starts Container
   |
kube-proxy exposes networking
   |
Application Running
```

---

### Responsibilities Summary

| Component | Responsibility |
|-----------|----------------|
| API Server | Entry point, validates requests |
| Scheduler | Assigns Pods to Worker Nodes |
| etcd | Stores cluster state |
| Controller Manager | Maintains desired state |
| Cloud Controller Manager | Integrates Kubernetes with cloud providers |
| kubelet | Creates and monitors Pods |
| Container Runtime | Runs containers |
| kube-proxy | Networking and Load Balancing |

---

### Easy Interview Mnemonic

**API Server**
> Accepts every request.

**Scheduler**
> Decides where Pods run.

**etcd**
> Stores everything.

**Controller Manager**
> Keeps desired state.

**Cloud Controller Manager**
> Connects Kubernetes with cloud providers.

**kubelet**
> Creates and manages Pods.

**Container Runtime**
> Runs containers.

**kube-proxy**
> Handles networking and load balancing.

---

### Quick Revision (30 Seconds)

Control Plane:
- API Server → Entry Point
- Scheduler → Assign Pods
- etcd → Database
- Controller Manager → Desired State
- Cloud Controller Manager → Cloud Integration

Worker Node:
- kubelet → Creates Pods
- Container Runtime → Runs Containers
- kube-proxy → Networking & Load Balancing

## Minikube

### 📖 What is Minikube?

**Minikube** is a tool that allows you to run a **single-node Kubernetes cluster** on your local machine.

It provides a lightweight Kubernetes environment for **learning, development, and testing** without requiring cloud infrastructure.

> **Definition**
>
> Minikube is a local Kubernetes implementation that creates a small Kubernetes cluster on your laptop or desktop.

---

### 🤔 Why Do We Need Minikube?

Setting up a real Kubernetes cluster requires:

- Multiple servers
- Networking configuration
- Control Plane
- Worker Nodes
- etcd
- kube-apiserver
- kube-scheduler
- kube-controller-manager
- kubelet
- kube-proxy
- Container Runtime

This setup is expensive and complex for beginners.

Minikube automates the entire setup with a single command:

```bash
minikube start
```

Within a few minutes, you have a fully functional Kubernetes cluster running locally.

---

### ⚙️ What Happens When You Run `minikube start`?

When you execute:

```bash
minikube start
```

Minikube performs the following steps:

1. Creates a Virtual Machine or Docker container.
2. Installs Kubernetes.
3. Starts the Control Plane.
4. Starts the Worker Node.
5. Configures networking.
6. Installs a Container Runtime.
7. Creates a Kubernetes cluster.

Finally, you'll see:

```text
😄  Kubernetes cluster is ready!
```

---

### 🏗️ What Components Does Minikube Install?

A Minikube node contains nearly all Kubernetes components.

```text
+----------------------------------------+
|               Minikube                 |
|                                        |
|  +----------------------------------+  |
|  | Control Plane                    |  |
|  |----------------------------------|  |
|  | kube-apiserver                   |  |
|  | etcd                             |  |
|  | kube-scheduler                   |  |
|  | kube-controller-manager          |  |
|  +----------------------------------+  |
|                                        |
|  +----------------------------------+  |
|  | Worker Components                |  |
|  |----------------------------------|  |
|  | kubelet                          |  |
|  | kube-proxy                       |  |
|  | Container Runtime                |  |
|  +----------------------------------+  |
|                                        |
+----------------------------------------+
```

Unlike a production cluster, all components run on a **single machine**.

---

### 🏢 Production Cluster vs Minikube

#### Production Kubernetes Cluster

```text
                Control Plane
              +----------------+
              | API Server     |
              | Scheduler      |
              | etcd           |
              +----------------+
                     |
     --------------------------------------
     |                |                  |
+---------+     +---------+        +---------+
| Worker1 |     | Worker2 |        | Worker3 |
+---------+     +---------+        +---------+
```

### Characteristics

- Dedicated Control Plane
- Multiple Worker Nodes
- High Availability
- Load Balancing
- Auto Scaling

---

#### Minikube Cluster

```text
+--------------------------------+
|          One Machine           |
|--------------------------------|
| API Server                     |
| Scheduler                      |
| etcd                           |
| kubelet                        |
| kube-proxy                     |
| Container Runtime              |
| Pods                           |
+--------------------------------+
```

Everything runs inside a single node.

---

### 🎯 Why Use Minikube?

Minikube is ideal for:

- Learning Kubernetes
- Practicing kubectl commands
- Developing applications
- Testing deployments
- Experimenting before deploying to the cloud

---

### ❌ Why Isn't Minikube Used in Production?

Minikube is designed only for development because:

- Single node by default
- No High Availability
- Limited scalability
- Runs on a local machine
- Not fault tolerant

Production environments require multiple nodes distributed across servers.

---

### 🖥️ What is a Node?

A **Node** is simply a machine where Kubernetes runs.

#### Production

```text
Node 1
Node 2
Node 3
Node 4
```

#### Minikube

```text
Node 1
```

Only one node exists by default.

That node acts as both:

- Control Plane
- Worker Node

---

#### 🏛️ Minikube Architecture

```text
                 Your Laptop
+--------------------------------------------------+
|                                                  |
|                Minikube                          |
|                                                  |
|   +------------------------------------------+   |
|   | Kubernetes Cluster                       |   |
|   |                                          |   |
|   |  Control Plane                           |   |
|   |  -------------------                     |   |
|   |  kube-apiserver                          |   |
|   |  etcd                                    |   |
|   |  kube-scheduler                          |   |
|   |  kube-controller-manager                 |   |
|   |                                          |   |
|   |  Worker Node                             |   |
|   |  --------------------                    |   |
|   |  kubelet                                |   |
|   |  kube-proxy                             |   |
|   |  Container Runtime                      |   |
|   |                                          |   |
|   |  Pods                                   |   |
|   +------------------------------------------+   |
|                                                  |
+--------------------------------------------------+
```

---

### 🔄 How Does Minikube Work with kubectl?

`kubectl` is the command-line client used to communicate with Kubernetes.

For example:

```bash
kubectl get pods
```

The request flows like this:

```text
You
 │
 │ kubectl get pods
 ▼
API Server
 │
 ▼
etcd (Stores Cluster State)
 │
 ▼
Response
 │
 ▼
kubectl displays Pods
```

The API Server is the entry point to Kubernetes. It communicates with other components (including etcd) and returns the requested information.

---

### 📦 Common Minikube Commands

| Command | Description |
|----------|-------------|
| `minikube start` | Start the cluster |
| `minikube stop` | Stop the cluster |
| `minikube delete` | Delete the cluster |
| `minikube status` | Check cluster status |
| `minikube ip` | Get the cluster IP |
| `minikube dashboard` | Open Kubernetes Dashboard |
| `kubectl get nodes` | List cluster nodes |
| `kubectl get pods -A` | List all pods in all namespaces |

---

### 👨‍💻 Can Minikube Create Multiple Nodes?

Yes.

By default, Minikube creates **one node**, but you can simulate a multi-node cluster.

```bash
minikube start --nodes=3
```

Architecture:

```text
             Control Plane
                   |
        ------------------------
        |          |           |
      Node1      Node2      Node3
```

This is useful for learning:

- Scheduling
- Networking
- Pod distribution
- High Availability concepts

---

### 📊 Minikube vs Production Kubernetes

| Feature | Minikube | Production Kubernetes |
|----------|----------|----------------------|
| Purpose | Learning & Development | Real Applications |
| Nodes | Usually 1 | Multiple |
| Runs On | Local Machine | Cloud / Data Center |
| High Availability | ❌ No | ✅ Yes |
| Auto Scaling | ❌ Limited | ✅ Yes |
| Cost | Free | Infrastructure Cost |
| Fault Tolerance | ❌ No | ✅ Yes |

---

### 📌 Key Takeaways

- Minikube is a lightweight local Kubernetes environment.
- It creates a Kubernetes cluster on your computer.
- It bundles both the Control Plane and Worker components.
- It is mainly used for learning, development, and testing.
- `kubectl` is the CLI used to interact with the Minikube cluster.
- Once you're comfortable with Minikube, you can easily transition to managed Kubernetes services like **Amazon EKS**, **Google GKE**, or **Azure AKS** because the Kubernetes concepts remain the same.

---

### 💡 Easy Analogy

Imagine Kubernetes as a city.

- **Production Kubernetes** is a large city with many buildings (worker nodes), roads (networking), and a central administration office (control plane).
- **Minikube** is a miniature model of that city built on your desk. It has all the essential parts but on a much smaller scale.

This makes Minikube the perfect playground for learning Kubernetes before managing real-world clusters.


## 📦 What is a Pod in Kubernetes?

A **Pod** is the **smallest deployable unit in Kubernetes**.

It is a wrapper (or container) that holds **one or more containers** and provides them with everything they need to run together, such as:

* Network (shared IP address)
* Storage (shared volumes)
* Configuration
* Environment variables

> **Definition**
>
> A **Pod** is the smallest unit that Kubernetes creates, schedules, and manages. It contains one or more tightly coupled containers that share the same network and storage.

---

# 🤔 Why Do We Need Pods?

You might wonder:

> **"Why doesn't Kubernetes manage containers directly?"**

The answer is because containers often need to work together.

For example, consider a web application:

```
Nginx (Web Server)
        │
        ▼
Node.js Application
```

Both containers need to:

* Communicate with each other
* Share files
* Start and stop together

Instead of managing them individually, Kubernetes groups them into a **Pod**.

---

# 🏗️ Pod Architecture

## Single Container Pod (Most Common)

```text
                 Pod
+-----------------------------------+
|                                   |
|   +---------------------------+   |
|   |      Nginx Container      |   |
|   +---------------------------+   |
|                                   |
| Shared Network                    |
| Shared Storage                    |
+-----------------------------------+
```

Most Pods contain **only one container**.

---

## Multi-Container Pod

```text
                  Pod
+------------------------------------------------+
|                                                |
| +-------------+   +------------------------+   |
| | Main App    |   | Logging Sidecar        |   |
| | Container   |   | Container              |   |
| +-------------+   +------------------------+   |
|                                                |
| Shared IP Address                              |
| Shared Storage                                 |
| Shared Network Namespace                       |
+------------------------------------------------+
```

Both containers:

* Run on the same machine.
* Share the same IP address.
* Can communicate using `localhost`.

---

# 🚢 Real-Life Analogy

Imagine you're moving to another city.

Instead of carrying each item separately:

* TV
* Bed
* Sofa
* Table

You pack everything into **one shipping container**.

```text
Items
 ├── TV
 ├── Sofa
 ├── Bed
 └── Table

        ▼

+-------------------+
| Shipping Container|
+-------------------+
```

Here:

* **Containers (TV, Sofa, etc.)** = Application Containers
* **Shipping Container** = Pod

Kubernetes moves and manages the **Pod**, not the individual containers.

---

# 🌐 Every Pod Gets Its Own IP

Each Pod has its own unique IP address.

Example:

```text
Node

+----------------------------------------+
|                                        |
| Pod A  ---> 10.244.0.2                 |
|                                        |
| Pod B  ---> 10.244.0.3                 |
|                                        |
| Pod C  ---> 10.244.0.4                 |
|                                        |
+----------------------------------------+
```

This allows Pods to communicate with each other over the cluster network.

---

# 🔗 Pods Share Resources

Containers inside the same Pod share:

### ✅ Network

* Same IP address
* Same port space
* Communicate using `localhost`

Example:

```text
App Container
      │
      ▼
localhost:8080
      ▲
      │
Logger Container
```

---

### ✅ Storage

Containers can share files using **Volumes**.

```text
          Pod
+--------------------------------+
|                                |
| App Container                  |
|        │                       |
|        ▼                       |
|     Shared Volume              |
|        ▲                       |
|        │                       |
| Backup Container               |
+--------------------------------+
```

---

# 🧠 Kubernetes Manages Pods, Not Containers

When you deploy an application, Kubernetes creates Pods.

For example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
    - name: nginx
      image: nginx
```

When you apply this configuration:

```bash
kubectl apply -f pod.yaml
```

Kubernetes creates:

```text
Pod
 └── Nginx Container
```

---

# 🔄 Pod Lifecycle

```text
      Created
          │
          ▼
      Pending
          │
          ▼
      Running
          │
          ▼
     Succeeded
          │
          ▼
      Deleted
```

Or, if something goes wrong:

```text
Created
    │
    ▼
Pending
    │
    ▼
Running
    │
    ▼
Failed
```

---

# ❓ Can We Have Multiple Containers in One Pod?

Yes, but only if they are **tightly coupled** and need to work together.

Examples:

* Main application + logging sidecar
* Main application + metrics exporter
* Web server + helper container

Avoid placing unrelated applications in the same Pod.

---

# 📦 Pod vs Container

| Feature                  | Container                                    | Pod                                                    |
| ------------------------ | -------------------------------------------- | ------------------------------------------------------ |
| What is it?              | A running application instance               | A wrapper around one or more containers                |
| Managed by               | Container Runtime (Docker, containerd, etc.) | Kubernetes                                             |
| Network                  | Own network namespace                        | Shared network namespace for all containers in the Pod |
| Storage                  | Independent unless configured                | Can share volumes                                      |
| Smallest Kubernetes Unit | ❌ No                                         | ✅ Yes                                                  |

---

# 📌 Important Commands

Create a Pod:

```bash
kubectl apply -f pod.yaml
```

View Pods:

```bash
kubectl get pods
```

Detailed information:

```bash
kubectl describe pod <pod-name>
```

Delete a Pod:

```bash
kubectl delete pod <pod-name>
```

Execute a command inside a Pod:

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

View Pod logs:

```bash
kubectl logs <pod-name>
```

---

# 💡 Key Takeaways

* A **Pod** is the **smallest deployable unit** in Kubernetes.
* A Pod contains **one or more containers**.
* Containers in the same Pod **share the same IP address, storage, and network**.
* Kubernetes **creates, schedules, scales, and manages Pods**, not individual containers.
* Most real-world Pods contain **a single container**, while multi-container Pods are used for closely related helper processes (such as logging or monitoring).

### 🚀 Easy Way to Remember

```text
Application
      │
      ▼
Container
      │
      ▼
Pod
      │
      ▼
Node
      │
      ▼
Cluster
```

**Hierarchy:**

* **Application** → Your software (e.g., Node.js, Spring Boot, Nginx)
* **Container** → Packages the application and its dependencies
* **Pod** → Wraps one or more containers
* **Node** → Runs Pods
* **Cluster** → A collection of Nodes managed by Kubernetes

