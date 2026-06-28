# k8s
kubernattes container orcstation plaform
kubernates by default cluster in nature

## Problem Solving
- single host nature
- auto scaling
- auto healing
- exterprise level standard support
  - load balancing
  - firewall
  - auto scaling
  - auto healing
  - api gateways
 
# Kubernetes Architecture Notes

# Kubernetes (K8s)

Kubernetes is an open-source container orchestration platform used to automate the deployment, scaling, and management of containerized applications.

---

# Kubernetes Architecture

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

# Control Plane Components

The Control Plane manages the entire Kubernetes cluster. It decides **what should run, where it should run, and continuously monitors the cluster state.**

---

## 1. API Server

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

## 2. Scheduler

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

## 3. etcd

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

## 4. Controller Manager

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

## 5. Cloud Controller Manager (CCM)

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

# Worker Node Components (Data Plane)

Worker Nodes actually run your applications.

---

## 1. kubelet

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

## 2. Container Runtime

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

## 3. kube-proxy

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

# Complete Workflow

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

# Responsibilities Summary

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

# Easy Interview Mnemonic

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

# Quick Revision (30 Seconds)

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
