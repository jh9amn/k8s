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
- [configMap in Kubernetes](#configMap-in-Kubernetes)
- [RBAC in kubernetes](#RBAC-in-kubernetes)
- [Monitoring in Kubernetes](#Monitoring-in-Kubernetes)
- [Kubernetes Custom Resources, CRDs, Controllers & Operators](#Kubernetes-Custom-Resources-CRDs-Controllers-&-Operators)
- [1. Why Kubernetes Needs Extension](#1-why-kubernetes-needs-extension)
- [2. Built-in Kubernetes Resources](#2-built-in-kubernetes-resources)
- [3. What is a Custom Resource (CR)?](#3-what-is-a-custom-resource-cr)
- [4. What is a Custom Resource Definition (CRD)?](#4-what-is-a-custom-resource-definition-crd)
- [5. CR vs CRD](#5-cr-vs-crd)
- [6. How CRDs Work Internally](#6-how-crds-work-internally)
- [7. Validation in CRDs](#7-validation-in-crds)
- [8. What is a Custom Controller?](#8-what-is-a-custom-controller)
- [9. Watchers (Informer)](#9-watchers-informer)
- [10. Reconciliation Loop](#10-reconciliation-loop)
- [11. client-go](#11-client-go)
- [12. controller-runtime](#12-controller-runtime)
- [13. Operator Pattern](#13-operator-pattern)
- [14. Operator SDK](#14-operator-sdk)
- [15. Istio VirtualService Example](#15-istio-virtualservice-example)
- [16. CNCF and Istio](#16-cncf-and-istio)
- [17. Kubernetes Sample Controller](#17-kubernetes-sample-controller)
- [18. Complete Workflow](#18-complete-workflow)
- [19. Interview Questions](#19-interview-questions)

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

-----

# configMap in Kubernetes

A **ConfigMap** in Kubernetes is an object used to **store non-sensitive configuration data** separately from your application code and container image.

Think of it like this:

> **ConfigMap = External configuration for your application**

Instead of hardcoding values like database host, port, or environment into your application, you keep them in a ConfigMap so you can change configuration without rebuilding the Docker image.

---

# Why do we need ConfigMaps?

Imagine your Python application has:

```python
DB_HOST = "localhost"
DB_PORT = "5432"
```

If tomorrow the database moves to another server, you'd have to:

1. Modify the code
2. Rebuild the Docker image
3. Push the image
4. Redeploy

This is inefficient.

Instead, store these values in a ConfigMap.

```
Application
     │
     ▼
Reads configuration
     │
     ▼
ConfigMap
```

Now you only update the ConfigMap and restart the Pod if necessary.

---

# Example ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: test-cm

data:
  db-host: mysql-service
  db-port: "3306"
  app-mode: production
```

Create it:

```bash
kubectl apply -f configmap.yaml
```

Check it:

```bash
kubectl get configmap
```

Describe it:

```bash
kubectl describe configmap test-cm
```

Output:

```
Name: test-cm

Data
====
db-host:
----
mysql-service

db-port:
----
3306

app-mode:
----
production
```

---

# Ways to use a ConfigMap

There are three common ways.

## 1. As Environment Variables (Most Common)

### ConfigMap

```yaml
data:
  db-host: mysql
  db-port: "3306"
```

Deployment:

```yaml
containers:
- name: python-app
  image: myapp:v1

  env:
    - name: DB_HOST
      valueFrom:
        configMapKeyRef:
          name: test-cm
          key: db-host

    - name: DB_PORT
      valueFrom:
        configMapKeyRef:
          name: test-cm
          key: db-port
```

Notice the important field:

```yaml
configMapKeyRef
```

**Not**

```yaml
configMapRef
```

This is exactly the issue you encountered earlier.

Inside the container:

```
DB_HOST=mysql

DB_PORT=3306
```

Python:

```python
import os

host = os.getenv("DB_HOST")
port = os.getenv("DB_PORT")
```

---

## 2. Import the Entire ConfigMap

Instead of specifying each key individually:

```yaml
envFrom:
  - configMapRef:
      name: test-cm
```

If ConfigMap contains:

```yaml
data:
  DB_HOST: mysql
  DB_PORT: "3306"
```

Then both variables become available automatically:

```
DB_HOST=mysql

DB_PORT=3306
```

---

## 3. Mount as a Volume

Kubernetes can create files from ConfigMap entries.

```yaml
volumes:
- name: config-volume
  configMap:
    name: test-cm

containers:
- volumeMounts:
  - mountPath: /etc/config
    name: config-volume
```

Inside the Pod:

```
/etc/config/
    db-host
    db-port
```

Each key becomes a file.

Example:

```
cat /etc/config/db-port
```

Output:

```
3306
```

---

# Create a ConfigMap from the command line

Instead of writing YAML:

```bash
kubectl create configmap test-cm \
  --from-literal=db-host=mysql \
  --from-literal=db-port=3306
```

Verify:

```bash
kubectl get configmap test-cm -o yaml
```

---

# Update a ConfigMap

Edit:

```bash
kubectl edit configmap test-cm
```

Or apply a modified YAML:

```bash
kubectl apply -f configmap.yaml
```

> **Note:** Pods using ConfigMaps as environment variables generally need to be restarted to pick up changes. Mounted ConfigMap volumes are updated automatically after a short delay.

---

# ConfigMap vs Secret

| Feature | ConfigMap                   | Secret                                    |
| ------- | --------------------------- | ----------------------------------------- |
| Purpose | Non-sensitive configuration | Sensitive data                            |
| Stores  | App settings, URLs, ports   | Passwords, API keys, tokens               |
| Encoded | Plain text                  | Base64-encoded (not encrypted by default) |
| Example | `DB_PORT=3306`              | `DB_PASSWORD=myPassword`                  |

Use a **ConfigMap** for:

* Database host
* Port numbers
* Application mode (`dev`, `test`, `prod`)
* Feature flags
* Log levels

Use a **Secret** for:

* Database passwords
* JWT secrets
* API tokens
* SSH keys
* TLS certificates

---

# Where ConfigMap fits in Kubernetes

```text
                    ConfigMap
               +----------------+
               | DB_HOST=mysql  |
               | DB_PORT=3306   |
               +-------+--------+
                       |
                       |
                Environment Variables
                       |
                       ▼
+--------------------------------------+
|              Pod                     |
|                                      |
|  +-------------------------------+   |
|  | Python Container              |   |
|  | DB_HOST=mysql                 |   |
|  | DB_PORT=3306                  |   |
|  +-------------------------------+   |
+--------------------------------------+
```

## Key points to remember

* A **ConfigMap** stores **non-sensitive configuration**.
* It helps separate configuration from your application image.
* You can consume it as:

  * Individual environment variables (`configMapKeyRef`)
  * All environment variables (`envFrom` with `configMapRef`)
  * Mounted files (volume)
* It makes applications easier to configure across development, testing, and production environments without rebuilding images.
----


# RBAC in kubernetes

**RBAC (Role-Based Access Control)** is Kubernetes' authorization mechanism that controls **who can perform what actions on which resources** in a cluster.

It allows cluster administrators to grant permissions to users, groups, or service accounts based on their roles.

> **RBAC = Define who can do what in a Kubernetes cluster**

---

# Why Do We Need RBAC?

Imagine a company where everyone has access to everything.

```text
Employee A

Can:
- Delete Production Pods ❌
- Modify Secrets ❌
- Create Namespaces ❌
```

This is risky.

Instead, different people should have different permissions.

Example:

- **Developer** → Deploy applications and view logs.
- **Tester** → View Pods and Services.
- **Administrator** → Full access to the cluster.

RBAC makes this possible.

---

# Real-Life Analogy

Think of a company office.

```text
Company

├── Employee
├── Manager
└── Administrator
```

Each role has different permissions:

| Person | Permissions |
|---------|-------------|
| Employee | View documents |
| Manager | View and edit documents |
| Administrator | Full control |

Similarly, Kubernetes assigns permissions using RBAC.

---

# How RBAC Works

```text
User

↓

Authentication
(Is the user valid?)

↓

RBAC Authorization
(What is the user allowed to do?)

↓

API Server

↓

Requested Resource
```

When you run:

```bash
kubectl delete pod nginx
```

The API Server checks:

1. Is the user authenticated?
2. Does RBAC allow deleting Pods?

If yes:

```text
Permission Granted ✅
```

Otherwise:

```text
Forbidden ❌
```

---

# RBAC Components

There are **four main RBAC resources**:

1. Role
2. ClusterRole
3. RoleBinding
4. ClusterRoleBinding

---

# 1. Role

A **Role** defines permissions **within a single namespace**.

Example:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

This Role allows:

- Get Pods
- List Pods
- Watch Pods

Only inside the **development** namespace.

---

# 2. ClusterRole

A **ClusterRole** defines permissions for:

- Cluster-wide resources (such as Nodes)
- All namespaces
- Or reusable permissions that can later be bound to a specific namespace

Example:

```yaml
kind: ClusterRole

rules:
- resources:
  - nodes
  verbs:
  - get
  - list
```

This allows viewing all nodes in the cluster.

---

# 3. RoleBinding

A **RoleBinding** assigns a **Role** to a user, group, or service account **within a namespace**.

```text
Role

↓

RoleBinding

↓

User
```

Example:

```yaml
kind: RoleBinding

subjects:
- kind: User
  name: aman

roleRef:
  kind: Role
  name: pod-reader
```

Now user **aman** can view Pods in that namespace.

---

# 4. ClusterRoleBinding

A **ClusterRoleBinding** assigns a **ClusterRole** to a user, group, or service account across the cluster.

```text
ClusterRole

↓

ClusterRoleBinding

↓

User
```

Example:

```yaml
kind: ClusterRoleBinding
```

This grants cluster-wide permissions.

---

# Complete RBAC Flow

```text
User

↓

kubectl get pods

↓

API Server

↓

RBAC

↓

Role / ClusterRole

↓

RoleBinding / ClusterRoleBinding

↓

Permission Granted

↓

Pods Returned
```

---

# Role vs ClusterRole

| Feature | Role | ClusterRole |
|---------|------|-------------|
| Scope | Single Namespace | Entire Cluster (or reusable across namespaces) |
| Access Nodes | ❌ | ✅ |
| Access Pods | ✅ | ✅ |
| Namespace Required | ✅ | ❌ |
| Typical Use | Namespace-specific permissions | Cluster-wide administration or shared permissions |

---

# RoleBinding vs ClusterRoleBinding

| Feature | RoleBinding | ClusterRoleBinding |
|---------|-------------|--------------------|
| Binds | Role or ClusterRole | ClusterRole |
| Scope | One Namespace | Entire Cluster |
| Used For | Team or project access | Cluster administrators and global permissions |

> **Note:** A `RoleBinding` can bind either a **Role** or a **ClusterRole**, but its permissions apply **only within its namespace**.

---

# Common Verbs

RBAC uses **verbs** to define allowed actions.

| Verb | Meaning |
|------|----------|
| get | Read one resource |
| list | List resources |
| watch | Watch for changes |
| create | Create resources |
| update | Modify resources |
| patch | Partially modify resources |
| delete | Delete resources |
| deletecollection | Delete multiple resources |

Example:

```yaml
verbs:
- get
- list
- watch
```

---

# Common Resources

Examples of resources you can secure with RBAC:

- Pods
- Deployments
- Services
- Secrets
- ConfigMaps
- Nodes
- Namespaces
- Jobs
- StatefulSets

---

# Real-World Example

Suppose your company has three teams.

### Developers

Can:

- Create Deployments
- View Pods
- View Logs

Cannot:

- Delete Namespaces
- Modify Secrets

---

### Testers

Can:

- View Pods
- View Services

Cannot:

- Deploy Applications

---

### Administrators

Can:

- Create Nodes
- Delete Nodes
- Manage Secrets
- Manage Namespaces
- Full cluster access

RBAC ensures each team gets only the permissions it needs (the **principle of least privilege**).

---

# Common Commands

Check if a user can perform an action:

```bash
kubectl auth can-i delete pods
```

Example:

```text
yes
```

Or:

```text
no
```

Check another user's permissions (if allowed):

```bash
kubectl auth can-i get pods --as=aman
```

View Roles:

```bash
kubectl get roles
```

View RoleBindings:

```bash
kubectl get rolebindings
```

View ClusterRoles:

```bash
kubectl get clusterroles
```

View ClusterRoleBindings:

```bash
kubectl get clusterrolebindings
```

---

# Easy Way to Remember

Think of RBAC like a bank.

- **Role** → Job description (Teller, Manager).
- **RoleBinding** → Employee assigned to that job.
- **ClusterRole** → Company-wide job description.
- **ClusterRoleBinding** → Company-wide assignment.

---

# Summary

| Component | Purpose |
|-----------|---------|
| **Role** | Defines permissions within a namespace |
| **ClusterRole** | Defines cluster-wide or reusable permissions |
| **RoleBinding** | Assigns a Role (or ClusterRole) within a namespace |
| **ClusterRoleBinding** | Assigns a ClusterRole across the entire cluster |
| **RBAC** | Controls who can perform which actions on Kubernetes resources |

> **In simple words:** **RBAC (Role-Based Access Control)** is Kubernetes' security system. It determines **who** (user, group, or service account) can perform **which actions** (create, view, update, delete) on **which resources** (Pods, Deployments, Services, Secrets, etc.), helping keep the cluster secure and organized.

----


# Monitoring in Kubernetes

**Kubernetes Monitoring** is the process of continuously collecting, analyzing, and visualizing the health, performance, and resource usage of your Kubernetes cluster and the applications running inside it.

Monitoring helps detect issues **before they affect users** and ensures applications remain healthy and performant.

> **Monitoring = Knowing the health and performance of your Kubernetes cluster in real time.**

---

# Why is Monitoring Important?

Imagine you have an e-commerce application running on Kubernetes.

Without monitoring:

```text
Pod crashes

↓

Nobody notices

↓

Website becomes unavailable

↓

Customers cannot place orders
```

With monitoring:

```text
Pod crashes

↓

Monitoring detects the issue

↓

Alert sent to DevOps Team

↓

Issue fixed quickly
```

---

# What Should You Monitor?

Monitoring can be divided into several categories.

### 1. Cluster Health

Monitor the health of the Kubernetes cluster.

Examples:

- Node Status
- Control Plane Health
- API Server
- Scheduler
- etcd

Commands:

```bash
kubectl get nodes
kubectl cluster-info
```

---

### 2. Node Monitoring

Monitor each Worker Node.

Metrics include:

- CPU Usage
- Memory Usage
- Disk Usage
- Network Usage

Example:

```bash
kubectl top nodes
```

Output:

```text
NAME       CPU(cores)   MEMORY(bytes)
worker-1   500m         2Gi
worker-2   250m         1.5Gi
```

---

### 3. Pod Monitoring

Monitor application Pods.

Things to check:

- Running Status
- Restarts
- CrashLoopBackOff
- Pending Pods

Commands:

```bash
kubectl get pods
kubectl describe pod <pod-name>
```

---

### 4. Container Monitoring

Monitor individual containers.

Examples:

- CPU Usage
- Memory Usage
- Restarts
- Logs

Command:

```bash
kubectl top pods
```

---

### 5. Application Monitoring

Monitor your application itself.

Examples:

- Response Time
- Request Rate
- Error Rate
- HTTP Status Codes
- Database Connections

---

### 6. Network Monitoring

Monitor:

- Service Connectivity
- Ingress Traffic
- DNS Resolution
- Network Latency
- Packet Loss

---

### 7. Storage Monitoring

Monitor:

- Persistent Volumes
- Disk Usage
- Read/Write Operations
- Storage Capacity

---

# Kubernetes Monitoring Architecture

```text
                    Kubernetes Cluster
                           │
     ---------------------------------------------
     │                                           │
 Worker Node 1                           Worker Node 2
     │                                           │
+------------+                           +------------+
| Pods       |                           | Pods       |
| kubelet    |                           | kubelet    |
+------------+                           +------------+
      │                                         │
      └─────────────── Metrics ──────────────────┘
                          │
                          ▼
                    Prometheus
                          │
               Stores Time-Series Data
                          │
                          ▼
                      Grafana
                          │
                    Dashboards
                          │
                          ▼
                     DevOps Team
```

---

# Popular Monitoring Tools

## 1. Metrics Server

The **Metrics Server** is the basic monitoring component in Kubernetes.

It collects:

- CPU Usage
- Memory Usage

Commands:

```bash
kubectl top nodes
kubectl top pods
```

Suitable for:

- Development
- Learning
- Horizontal Pod Autoscaler (HPA)

---

## 2. Prometheus

Prometheus is the **most popular monitoring system** for Kubernetes.

It:

- Collects metrics
- Stores time-series data
- Supports powerful queries using **PromQL**
- Triggers alerts

Collects metrics like:

- CPU
- Memory
- Network
- Disk
- Pod Restarts
- HTTP Requests

---

## 3. Grafana

Grafana is used to **visualize metrics** collected by Prometheus.

Features:

- Beautiful dashboards
- Real-time graphs
- Alerts
- Reports

Example Dashboard:

```text
CPU Usage

███████████

Memory Usage

████████

Network

█████
```

---

## 4. Alertmanager

Alertmanager works with Prometheus.

Responsibilities:

- Send Email Alerts
- Slack Notifications
- Microsoft Teams Notifications
- PagerDuty Alerts

Example:

```text
CPU > 90%

↓

Alertmanager

↓

Email

↓

DevOps Engineer
```

---

## 5. ELK Stack (Logging)

Monitoring is often combined with logging.

ELK stands for:

- Elasticsearch
- Logstash
- Kibana

Used for:

- Collecting logs
- Searching logs
- Visualizing logs

---

# Metrics vs Logs

| Metrics | Logs |
|----------|------|
| Numerical data | Text data |
| CPU Usage | Application Errors |
| Memory Usage | Stack Traces |
| Disk Usage | Login Events |
| Request Count | Exception Messages |

Both are important for troubleshooting.

---

# Monitoring Workflow

```text
Application

↓

Metrics Generated

↓

Prometheus

↓

Grafana Dashboard

↓

Alertmanager

↓

DevOps Team
```

---

# Common kubectl Commands

Check Nodes:

```bash
kubectl get nodes
```

Check Pods:

```bash
kubectl get pods
```

CPU & Memory:

```bash
kubectl top nodes
kubectl top pods
```

Describe Pod:

```bash
kubectl describe pod <pod-name>
```

View Logs:

```bash
kubectl logs <pod-name>
```

View Events:

```bash
kubectl get events
```

---

# Real-World Example

Suppose your application suddenly becomes slow.

Monitoring Dashboard shows:

```text
CPU Usage

95%

Memory

85%

Pod Restarts

12

HTTP Errors

500
```

From these metrics, you can identify:

- High CPU usage
- Frequent Pod crashes
- Increased server errors

You might then:

- Scale the Deployment
- Investigate application logs
- Increase resource limits
- Fix the underlying application issue

---

# Monitoring Best Practices

✅ Monitor Nodes

✅ Monitor Pods

✅ Monitor Containers

✅ Monitor Application Performance

✅ Set Alerts for Critical Metrics

✅ Collect Logs

✅ Create Dashboards

✅ Regularly Review Cluster Health

---

# Interview Tip

**Q:** Which tools are commonly used for Kubernetes monitoring?

**Answer:**

- **Metrics Server** – Basic CPU and memory metrics.
- **Prometheus** – Collects and stores metrics.
- **Grafana** – Visualizes metrics with dashboards.
- **Alertmanager** – Sends alerts based on rules.
- **ELK Stack** (or alternatives like Loki) – Collects and analyzes logs.

---

# Summary

| Tool | Purpose |
|------|---------|
| **Metrics Server** | Basic CPU and Memory Metrics |
| **Prometheus** | Collect Metrics & Time-Series Storage |
| **Grafana** | Dashboards & Visualization |
| **Alertmanager** | Alert Notifications |
| **ELK Stack / Loki** | Centralized Logging |

> **In simple words:** Kubernetes monitoring is the process of tracking the health and performance of your cluster, nodes, Pods, containers, and applications. In production, the most common monitoring stack is **Prometheus + Grafana + Alertmanager**, while **ELK Stack** or **Loki** is commonly used for centralized logging.


------

# Kubernetes Custom Resources CRDs Controllers & Operators

# 1. Why Kubernetes Needs Extension

Kubernetes already provides many built-in resources.

Examples:

- Pod
- Deployment
- Service
- ConfigMap
- Secret
- Namespace

But every company has different requirements.

Imagine you want Kubernetes to manage:

- Database clusters
- Kafka clusters
- Machine Learning models
- Certificates
- DNS records
- Cloud resources

Kubernetes doesn't know these resources.

So Kubernetes allows us to create our own resources.

These are called **Custom Resources**.

---

# 2. Built-in Kubernetes Resources

Example:

```yaml
kind: Pod
```

```yaml
kind: Deployment
```

```yaml
kind: Service
```

These resource types are already understood by Kubernetes.

---

# 3. What is a Custom Resource (CR)?

A **Custom Resource (CR)** is a **new Kubernetes object** created by users after defining a CRD.

Think of it like creating your own Kubernetes object.

Example:

Instead of

```yaml
kind: Deployment
```

You create

```yaml
kind: Database
```

or

```yaml
kind: VirtualService
```

These are not built into Kubernetes.

They become available after installing a CRD.

---

## Real Life Analogy

Imagine Microsoft Word.

Built-in:

- Heading
- Table
- Paragraph

Now imagine a plugin adds

- UML Diagram
- Mind Map

Those new objects are like Custom Resources.

---

# 4. What is a Custom Resource Definition (CRD)?

A **CRD** tells Kubernetes about a **new resource type**.

Think of it as:

> "Hey Kubernetes, from today onward, understand this new object."

Example:

```yaml
kind: CustomResourceDefinition
```

Once installed

Kubernetes understands

```yaml
kind: Database
```

or

```yaml
kind: VirtualService
```

---

## CRD Responsibilities

- Register new API
- Register new Kind
- Define Schema
- Validate Input
- Store object inside etcd

---

# 5. CR vs CRD

| CRD | CR |
|------|----|
| Defines new resource type | Instance of resource |
| Installed once | Created many times |
| Like a Class | Like an Object |
| Example: Database Definition | Example: Production Database |

---

Example

CRD

```text
Car
```

CR

```text
BMW

Audi

Tesla
```

---

# 6. How CRDs Work Internally

```
User

↓

kubectl apply CRD

↓

API Server

↓

Registers new API

↓

etcd stores schema

↓

Now Kubernetes understands new object
```

---

# 7. Validation in CRDs

CRDs support validation using OpenAPI Schema.

Example

```yaml
age:
  type: integer
```

Now

```yaml
age: hello
```

will fail.

Validation prevents invalid objects.

Example validations

- string
- integer
- boolean
- enum
- minimum
- maximum
- required fields

---

# 8. What is a Custom Controller?

Creating a CRD only stores objects.

Nothing happens automatically.

A **Controller** watches Custom Resources and performs actions.

Example

```
Database CR created

↓

Controller sees it

↓

Creates Pod

↓

Creates PVC

↓

Creates Service

↓

Database becomes ready
```

Controller = Brain

---

# 9. Watchers (Informer)

A Controller continuously watches Kubernetes.

Events

```
Create

Update

Delete
```

Whenever one happens

Controller reacts.

Example

```
VirtualService Updated

↓

Controller notices

↓

Updates Envoy configuration
```

---

# 10. Reconciliation Loop

Controllers continuously compare

Desired State

vs

Actual State

Example

Desired

```
3 Pods
```

Actual

```
2 Pods
```

Controller

↓

Creates one Pod.

This continuous checking is called

**Reconciliation Loop**

---

# 11. client-go

client-go is the **official Go library** for Kubernetes.

It allows Go programs to communicate with Kubernetes.

Example

```go
clientset.CoreV1().
Pods("default").
List(...)
```

Used for

- Create Pods
- Delete Pods
- Watch Resources
- Read Resources

Every controller written in Go uses client-go.

---

# 12. controller-runtime

Writing controllers using client-go is complicated.

controller-runtime is a higher-level framework.

It provides

- Manager
- Cache
- Reconciler
- Event handling
- Leader election

Much less code.

Most modern controllers use

controller-runtime.

---

# 13. Operator Pattern

Operator =

CRD

+

Custom Controller

Business Logic

Operator automates complex applications.

Example

```
PostgreSQL Operator

↓

Database CR

↓

Automatically

Creates DB

Backups

Recovery

Scaling

Upgrade
```

Operators replace manual administration.

---

# 14. Operator SDK

Operator SDK is a framework for building Operators.

It provides

- Project scaffolding
- CRD generation
- Controller generation
- Testing
- Packaging

Supported Languages

- Go
- Helm
- Ansible

---

# 15. Istio VirtualService Example

Istio adds a CRD called

```
VirtualService
```

Example

```yaml
kind: VirtualService
```

Example

```
api.example.com

↓

Route

↓

Version v1
```

or

```
90%

↓

v1

10%

↓

v2
```

This enables

- Traffic Splitting
- Canary Deployment
- A/B Testing
- Retry
- Timeout

Without changing application code.

---

# 16. CNCF and Istio

CNCF

=

Cloud Native Computing Foundation

It hosts many cloud-native projects.

Examples

- Kubernetes
- Prometheus
- Envoy
- Fluentd
- OpenTelemetry
- Helm
- Harbor
- containerd
- etcd

Istio is a CNCF graduated project (through its stewardship with the community and Envoy ecosystem).

Istio provides

- Service Mesh
- Traffic Management
- Security
- Observability

---

# 17. Kubernetes Sample Controller

The Kubernetes project provides a Sample Controller.

Purpose

Learn how Controllers work.

Repository

```
kubernetes/sample-controller
```

It demonstrates

- CRD
- Controller
- Watchers
- Informers
- Reconciliation

Useful for beginners.

---

# 18. Complete Workflow

```
Developer

↓

Create CRD

↓

Install CRD

↓

API Server registers new API

↓

Create Custom Resource

↓

Stored in etcd

↓

Controller watches

↓

Controller receives event

↓

Reconcile()

↓

Creates Pods

↓

Creates Services

↓

Creates PVC

↓

Application Running
```

---

# Complete Architecture

```
                  Kubernetes Cluster

                       API Server
                           │
          ------------------------------
          │                            │
         etcd                 Custom Controller
                                    │
                           Watches CR Events
                                    │
          -----------------------------------------
          │                 │                     │
       CREATE            UPDATE               DELETE
                                    │
                                    ▼
                              Reconcile()
                                    │
                                    ▼
                    Creates Kubernetes Resources
                           (Pods, Services,
                        Deployments, PVC, etc.)
                                    │
                                    ▼
                             Application Running
```

---

# Summary

| Term | Description |
|------|-------------|
| Custom Resource (CR) | Instance of a custom Kubernetes object |
| CRD | Registers a new Kubernetes resource type |
| Validation | Ensures Custom Resources have valid data |
| Custom Controller | Watches CRs and performs actions |
| Watchers | Detect Create, Update, Delete events |
| Reconciliation | Makes actual state match desired state |
| client-go | Official Go client for Kubernetes |
| controller-runtime | Simplifies writing controllers |
| Operator | CRD + Controller + Business Logic |
| Operator SDK | Framework for building Operators |
| VirtualService | Istio Custom Resource for traffic routing |
| Istio | Service Mesh built on Kubernetes |
| CNCF | Organization hosting cloud-native projects |
| Sample Controller | Reference project for learning Kubernetes controllers |

---

# Easy Way to Remember

```
CRD
│
├── Introduces a new resource type
│
▼
CR
│
├── Creates an object of that type
│
▼
Controller
│
├── Watches the object
│
▼
Reconcile()
│
├── Creates or updates Kubernetes resources
│
▼
Application Runs
```

### One-line memory trick

- **CRD** → *Defines a new Kubernetes resource type.*
- **CR** → *An object created from that type.*
- **Controller** → *Watches CRs and keeps the cluster in the desired state.*
- **Operator** → *A smart controller that automates application management.*
