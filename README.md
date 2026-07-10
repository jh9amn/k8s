# Kubernetes Notes

## 📚 Table of Contents

- [Introduction](#introduction)
- [What is Kubernetes?](#what-is-kubernetes)
- [Kubernetes Architecture](#Kubernetes-Architecture)
- [Minikube](#minikube)
- [What is a Pod in Kubernetes?](#What-is-a-Pod-in-Kubernetes)
- 📄 [Kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/quick-reference/)
- 📖 [Kubernetes Documentation](https://kubernetes.io/docs/)
- 🚀 [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
- [How kubernates implement autohealing property](#How-kubernates-implement-autohealing-property)
- [Kubernetes Services](#kubernetes-services)
  - [What is a Service?](#what-is-a-service)
  - [Why Do We Need a Service?](#why-do-we-need-a-service)
  - [How Services Work Internally](#how-services-work-internally)
  - [Labels and Selectors](#labels-and-selectors)
  - [Service Architecture](#service-architecture)
  - [kube-proxy and Load Balancing](#kube-proxy-and-load-balancing)
  - [Types of Services](#types-of-services)
    - [ClusterIP](#clusterip)
    - [NodePort](#nodeport)
    - [LoadBalancer](#loadbalancer)
    - [ExternalName](#externalname)
  - [Example YAML](#example-yaml)
  - [Services summary](#services-summary)
- [What is Ingress in Kubernetes?](#What-is-Ingress-in-Kubernetes)

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


# What is a Pod in Kubernetes?

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

## How kubernates implement autohealing property

Kubernetes provides **self-healing (auto-healing)** by constantly comparing the **desired state** of your application with the **current state** of the cluster. If they don't match, Kubernetes automatically takes action to restore the desired state.

Let's understand this from the inside.

---

# 1. Desired State vs Current State

When you create a deployment like this:

```yaml
apiVersion: apps/v1
kind: Deployment

spec:
  replicas: 3

  template:
    spec:
      containers:
      - name: nginx
        image: nginx
```

You are telling Kubernetes:

> "I always want **3 running nginx pods**."

This is called the **Desired State**.

Suppose currently the cluster has

```
Pod 1  Running
Pod 2  Running
Pod 3  Running
```

Current State = Desired State

Everything is fine.

---

# 2. What if one pod crashes?

Suppose Pod 2 crashes.

Current state becomes

```
Pod 1  Running
Pod 2  Crashed ❌
Pod 3  Running
```

Now Kubernetes notices

```
Desired Pods = 3
Current Pods = 2
```

Mismatch detected.

---

# 3. Who detects this?

The **Kubernetes Controller Manager**.

Inside Controller Manager there are multiple controllers.

For Deployments:

```
Deployment Controller
        ↓
ReplicaSet Controller
        ↓
Pods
```

ReplicaSet Controller continuously checks

```
Desired replicas = 3

Actual replicas = 2

Need = +1 pod
```

It immediately asks the API Server

```
Create one more Pod.
```

---

# Internal Flow

```
User

kubectl apply -f deployment.yaml
          │
          ▼
API Server
          │
          ▼
etcd
Stores:
replicas = 3
```

Controller Manager continuously watches the API Server.

```
Controller Manager

Desired = 3

Current = 2

Need = Create 1 Pod
```

Then

```
Scheduler

Find a healthy node
```

Suppose Node 2 has free memory.

```
Scheduler

Assign Pod

↓

Node 2
```

Then kubelet on Node 2 receives

```
Create nginx container
```

Container Runtime

```
Pull Image

↓

Create Container

↓

Pod Running
```

Now again

```
Pod1 Running
Pod2 Running (new)
Pod3 Running
```

Desired == Current

Cluster becomes healthy again.

---

# Complete Architecture

```text
               kubectl apply
                     │
                     ▼
              +---------------+
              |   API Server  |
              +---------------+
                     │
                     ▼
                +---------+
                |  etcd   |
                +---------+
                     ▲
                     │
     watches desired state
                     │
       +--------------------------+
       | Controller Manager       |
       | ReplicaSet Controller    |
       +--------------------------+
                     │
      Desired = 3
      Current = 2
                     │
          Create New Pod
                     ▼
             +---------------+
             |   Scheduler   |
             +---------------+
                     │
         Select Healthy Node
                     ▼
         +--------------------+
         |      Kubelet       |
         +--------------------+
                     │
          Container Runtime
                     │
                     ▼
               New Pod Running
```

---

# 4. What if the Node itself dies?

Suppose

```
Node 1
```

suddenly loses power.

Pods running there become unreachable.

```
Node 1 ❌

Pod A
Pod B
```

The **Node Controller** (inside the Controller Manager) notices that the node has stopped sending heartbeat messages.

```
Node Status

Ready = False
```

The Node Controller marks it as unavailable.

The ReplicaSet then observes:

```
Desired Pods = 3

Available Pods = 1
```

It creates replacement pods on healthy nodes.

```
Node 2

Pod A (new)

Node 3

Pod B (new)
```

---

# 5. How does Kubernetes know a Pod is unhealthy?

Kubernetes uses **Probes**.

### Liveness Probe

Checks:

> Is the application still alive?

Example:

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
```

If the application stops responding:

```
Health Check

↓

Fail

↓

kubelet kills container

↓

Container restarted
```

---

### Readiness Probe

Checks:

> Is the application ready to receive traffic?

If not ready:

```
Service

↓

Remove Pod from Load Balancer
```

Traffic is no longer sent to that pod until it becomes ready again.

---

### Startup Probe

Useful for applications that take a long time to start.

```
App Starting...

↓

Startup Probe

↓

Only after success

↓

Liveness Probe begins
```

This prevents Kubernetes from restarting slow-starting applications too early.

---

# 6. Who actually restarts the container?

The **kubelet** on each node.

It constantly watches the Pod specification.

If a container exits unexpectedly:

```
Container

↓

Crash

↓

kubelet

↓

Restart Container
```

This is controlled by the Pod's `restartPolicy` (for Deployments, it is effectively `Always`).

---

# 7. Example Timeline

```text
Time 0
------
Pod Running

Time 5
------
Application crashes

Time 6
------
Liveness Probe fails

Time 7
------
kubelet restarts container

Time 8
------
Container Running

Time 10
-------
If restart fails repeatedly

ReplicaSet creates replacement Pod
```

---

# Components Responsible for Self-Healing

| Component                             | Responsibility                                                            |
| ------------------------------------- | ------------------------------------------------------------------------- |
| API Server                            | Stores and exposes the desired state                                      |
| etcd                                  | Persists the cluster state                                                |
| Controller Manager                    | Detects differences between desired and current state and reconciles them |
| ReplicaSet Controller                 | Ensures the required number of Pod replicas are running                   |
| Node Controller                       | Detects failed or unreachable nodes                                       |
| Scheduler                             | Places replacement Pods on suitable nodes                                 |
| kubelet                               | Restarts failed containers and reports Pod status                         |
| Probes (Liveness, Readiness, Startup) | Detect unhealthy or unready applications                                  |

---

## Summary

Kubernetes' self-healing works through a continuous **reconciliation loop**:

1. You declare the **desired state** (e.g., 3 replicas).
2. The **API Server** stores that state in **etcd**.
3. The **Controller Manager** continuously compares the desired state with the actual state.
4. If Pods crash or Nodes fail, controllers detect the mismatch.
5. The **Scheduler** assigns replacement Pods to healthy nodes.
6. The **kubelet** starts or restarts containers as needed.
7. Health probes help Kubernetes detect unhealthy applications and recover them automatically.

This continuous monitoring and reconciliation is the core reason Kubernetes can automatically recover from many common failures without manual intervention.

# ```Kubernetes Services```

----

# What is a Service?

A **Service** in Kubernetes is an abstraction that provides a **stable network endpoint** for a set of Pods. Since Pods are **ephemeral** (they can be created, destroyed, or replaced at any time), their IP addresses change. A Service solves this by giving clients a **fixed IP address and DNS name** to communicate with.

---

# Why do we need a Service?

Imagine you have a Deployment with 3 Pods.

```text
          Deployment
               │
      ┌────────┴────────┐
      │                 │
 ReplicaSet         replicas = 3
      │
 ┌────┼────┐
 │    │    │
Pod1 Pod2 Pod3

IP: 10.0.0.2
IP: 10.0.0.5
IP: 10.0.0.8
```

Suppose another application wants to call these Pods.

It may connect to:

```
10.0.0.2
```

But what if Pod1 crashes?

```
Pod1 ❌

New Pod Created

IP = 10.0.0.12
```

Now the client is still trying to reach:

```
10.0.0.2
```

which no longer exists.

This is the problem.

---

# Service solves this

A Service creates one **virtual IP (ClusterIP)**.

```text
               Service

          10.96.0.25

               │
        ┌──────┼──────┐
        │      │      │
      Pod1   Pod2   Pod3
```

Now clients only communicate with:

```
10.96.0.25
```

Even if Pods change, the Service automatically routes traffic to the healthy Pods.

---

# How does Service know which Pods belong to it?

Using **labels** and **selectors**.

Pods:

```yaml
metadata:
  labels:
    app: nginx
```

Service:

```yaml
selector:
  app: nginx
```

The Service continuously finds all Pods with:

```
app = nginx
```

No matter how many Pods are created or deleted.

---

# Internal Working

### Step 1

Deployment creates Pods.

```
Deployment

↓

Pod A
app=nginx

↓

Pod B
app=nginx

↓

Pod C
app=nginx
```

---

### Step 2

Service watches Pods with the label:

```
app=nginx
```

It builds an endpoint list.

```
Service

↓

Endpoints

10.0.0.2

10.0.0.5

10.0.0.8
```

---

### Step 3

A client sends a request.

```
Client

↓

Service IP

10.96.0.25
```

---

### Step 4

The Service forwards the request to one of the Pods.

```
Service

↓

Pod A

or

Pod B

or

Pod C
```

This acts as **load balancing**.

---

# How is load balancing done?

The Service doesn't directly forward packets. Instead, **kube-proxy** running on each node programs networking rules (using technologies like `iptables`, `IPVS`, or eBPF depending on the cluster setup).

```text
Client
   │
   ▼
Service IP
   │
   ▼
kube-proxy
   │
 ┌─┼───────────┐
 │ │           │
 ▼ ▼           ▼
Pod1        Pod2       Pod3
```

Each request is routed to one of the healthy Pods.

---

# Architecture

```text
                 Client
                    │
                    ▼
             Service (Virtual IP)
             10.96.0.25
                    │
             kube-proxy
                    │
         ┌──────────┼───────────┐
         ▼          ▼           ▼
      Pod1       Pod2       Pod3
```

---

# What if a Pod dies?

Suppose

```
Pod2 crashes.
```

ReplicaSet creates a new Pod.

```
Pod4

IP = 10.0.0.20
```

The Service updates its endpoints automatically.

Old list:

```
10.0.0.2

10.0.0.5

10.0.0.8
```

New list:

```
10.0.0.2

10.0.0.8

10.0.0.20
```

Clients continue using the same Service IP.

---

# Example YAML

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx

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

---

### Service

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  selector:
    app: nginx

  ports:
  - port: 80
    targetPort: 80

  type: ClusterIP
```

---

# Types of Services

| Service Type            | Accessible From                                | Use Case                                      |
| ----------------------- | ---------------------------------------------- | --------------------------------------------- |
| **ClusterIP** (default) | Inside the cluster only                        | Communication between Pods/services           |
| **NodePort**            | Outside the cluster via `<NodeIP>:<NodePort>`  | Testing or simple external access             |
| **LoadBalancer**        | External clients through a cloud load balancer | Production applications on cloud platforms    |
| **ExternalName**        | Maps the Service to an external DNS name       | Access external services using Kubernetes DNS |

---

# ClusterIP

```text
Pod A

↓

Service

↓

Pod B
```

Accessible only within the cluster.

---

# NodePort

```text
Internet

↓

Node IP:30080

↓

Service

↓

Pods
```

The Service is reachable through a port opened on every node.

---

# LoadBalancer

```text
Internet

↓

Cloud Load Balancer

↓

Service

↓

Pods
```

Common in managed Kubernetes services (AWS, Azure, GCP).

---

# ExternalName

```text
Pod

↓

database-service

↓

db.example.com
```

No proxying is done—the Service simply returns the external DNS name.

---

# Services summary

A **Service** is a stable networking layer that sits in front of Pods.

It provides:

* A **stable IP address and DNS name** even when Pods are replaced.
* **Automatic discovery** of Pods using labels and selectors.
* **Load balancing** of traffic across healthy Pods.
* Different exposure options through **ClusterIP**, **NodePort**, **LoadBalancer**, and **ExternalName**.

Without a Service, clients would need to know the changing IP addresses of Pods. With a Service, they only need to know the Service name or IP, while Kubernetes handles routing to the appropriate Pods automatically.


----


# What is Ingress in Kubernetes?

An **Ingress** is a Kubernetes API object that manages **external HTTP and HTTPS traffic** to services inside a Kubernetes cluster.

Instead of exposing every application using a separate **NodePort** or **LoadBalancer**, an Ingress lets you expose **multiple services through a single entry point**.

> **Ingress = Smart traffic router for HTTP/HTTPS requests**

---

# Why Do We Need Ingress?

Imagine you have three applications running in your cluster:

- Frontend
- Backend API
- Admin Panel

Without Ingress, you might create three separate LoadBalancer Services.

```text
Internet

 ├── LoadBalancer 1 → Frontend
 ├── LoadBalancer 2 → Backend
 └── LoadBalancer 3 → Admin
```

Problems:

- More cloud resources
- Higher cost
- Multiple public IP addresses
- Difficult to manage

---

With Ingress:

```text
Internet
      │
      ▼
+----------------------+
|   Ingress Controller |
+----------------------+
      │
 ┌────┴─────┐
 │          │
 ▼          ▼
Frontend   Backend
            │
            ▼
         Admin
```

Only **one public IP** is needed, and the Ingress routes traffic to the correct Service.

---

# Real-Life Analogy

Imagine a large office building.

```text
Visitor

↓

Reception Desk

↓

HR Department

↓

Finance Department

↓

Engineering Department
```

- Visitor = User Request
- Reception = Ingress
- Departments = Kubernetes Services

The receptionist decides where each visitor should go.

Similarly, Ingress routes incoming requests to the appropriate Service.

---

# How Ingress Works

```text
User

↓

Ingress

↓

Service

↓

Pod
```

Example:

```text
https://example.com

↓

Ingress

↓

Frontend Service

↓

Frontend Pods
```

---

# Path-Based Routing

Ingress can route traffic based on the URL path.

Example:

```text
example.com/

↓

Frontend
```

```text
example.com/api

↓

Backend API
```

Architecture:

```text
Internet

↓

Ingress

├── /      → Frontend Service
├── /api   → Backend Service
└── /admin → Admin Service
```

---

# Host-Based Routing

Ingress can also route traffic based on the hostname.

Example:

```text
shop.example.com

↓

Shopping Service
```

```text
blog.example.com

↓

Blog Service
```

Architecture:

```text
Internet

↓

Ingress

├── shop.example.com
│      │
│      ▼
│   Shop Service
│
└── blog.example.com
       │
       ▼
   Blog Service
```

---

# Example Ingress YAML

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
spec:
  rules:
  - host: myapp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
```

This means:

```text
myapp.com

↓

Frontend Service

↓

Frontend Pods
```

---

# What is an Ingress Controller?

An **Ingress resource** only defines the routing rules.

The **Ingress Controller** is the software that reads those rules and actually routes the traffic.

Without an Ingress Controller, creating an Ingress resource has **no effect**.

Popular Ingress Controllers:

- NGINX Ingress Controller
- HAProxy Ingress Controller
- Traefik
- Kong
- AWS Load Balancer Controller

---

# Complete Request Flow

```text
User

↓

DNS

↓

Ingress Controller

↓

Ingress Rules

↓

Service

↓

Pod
```

---

# Features of Ingress

### 1. Path-Based Routing

```text
/app

↓

App Service
```

---

### 2. Host-Based Routing

```text
api.example.com

↓

API Service
```

---

### 3. SSL/TLS Termination

Ingress can terminate HTTPS connections.

```text
HTTPS

↓

Ingress

↓

HTTP

↓

Pods
```

This allows you to manage SSL certificates in one place.

---

### 4. Load Balancing

Ingress distributes requests among multiple Pods.

```text
Ingress

↓

Pod 1

Pod 2

Pod 3
```

---

### 5. URL Rewriting

Example:

```text
/api/users

↓

/users
```

Useful when backend applications expect different URL structures.

---

# Ingress vs Service

| Feature | Service | Ingress |
|---------|----------|----------|
| Purpose | Exposes Pods | Routes external HTTP/HTTPS traffic |
| Layer | Network (Layer 4 - TCP/UDP) | Application (Layer 7 - HTTP/HTTPS) |
| Supports Path Routing | ❌ | ✅ |
| Supports Host Routing | ❌ | ✅ |
| SSL/TLS Termination | ❌ | ✅ |
| Requires Ingress Controller | ❌ | ✅ |

---

# Service vs Ingress Architecture

Without Ingress:

```text
Internet

├── LoadBalancer → Frontend
├── LoadBalancer → Backend
└── LoadBalancer → Admin
```

With Ingress:

```text
Internet

↓

LoadBalancer

↓

Ingress Controller

↓

Ingress Rules

├── Frontend Service
├── Backend Service
└── Admin Service
```

---

# When Should You Use Ingress?

Use Ingress when:

- Hosting multiple applications.
- Using one domain for multiple services.
- You need path-based routing.
- You need host-based routing.
- You want HTTPS termination.
- You want to reduce the number of cloud load balancers.

---

# Easy Way to Remember

- **Service** = Connects users to a specific set of Pods.
- **Ingress** = Decides **which Service** should receive an incoming HTTP/HTTPS request.
- **Ingress Controller** = The software that enforces the Ingress rules.

Think of it like this:

```text
Internet

↓

Ingress (Traffic Manager)

↓

Service (Reception Desk)

↓

Pods (Employees)
```

---

# Interview Tip

A common interview question is:

**Q:** Can Ingress expose Pods directly?

**Answer:**

**No.** An Ingress always routes traffic to a **Service**, and the Service forwards it to the appropriate Pods.

---

# Summary

| Component | Responsibility |
|-----------|----------------|
| **Ingress** | Defines HTTP/HTTPS routing rules |
| **Ingress Controller** | Implements the routing rules |
| **Service** | Provides a stable endpoint for Pods |
| **Pod** | Runs the application |

> **In simple words:** An **Ingress** is a smart HTTP/HTTPS router for Kubernetes. It allows multiple applications to share a single external IP address and routes requests to the correct Service based on the request's host or URL path.



