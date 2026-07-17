# 📚 01 - Cluster & Minikube

> Before learning Pods, Deployments, and Services, you must understand **what a Kubernetes Cluster is**, **how it works internally**, and **how Minikube simulates a real Kubernetes environment**.

---

# 📖 Table of Contents

- What is a Kubernetes Cluster?
- Why Do We Need a Cluster?
- Cluster Architecture
- Control Plane
- Worker Nodes
- How a Request Travels Inside Kubernetes
- What is Minikube?
- Minikube Architecture
- Installing & Starting Minikube
- Frequently Used Minikube Commands
- Common Errors
- Pro Tips
- Interview Questions
- Quick Revision

---

# What is a Kubernetes Cluster?

A **Kubernetes Cluster** is a group of computers (called **Nodes**) that work together to run containerized applications.

Think of the cluster as **one large computer**, even though it's made up of many machines.

```
                   Kubernetes Cluster
        +--------------------------------------+
        |                                      |
        |      Control Plane                   |
        |             │                        |
        |             ▼                        |
        |    Schedules & Manages Pods          |
        |                                      |
        |      Worker Node 1                   |
        |      Worker Node 2                   |
        |      Worker Node 3                   |
        |                                      |
        +--------------------------------------+
```

The cluster is made up of:

- **Control Plane (Master Node)** → Makes decisions.
- **Worker Nodes** → Run your applications.

---

# Real-World Analogy

Imagine you own a food delivery company.

```
CEO
 │
 ▼
Managers
 │
 ▼
Delivery Boys
```

In Kubernetes:

```
CEO
=
Control Plane

Managers
=
Scheduler + Controller Manager

Delivery Boys
=
Worker Nodes

Food Packages
=
Containers

Delivery Bag
=
Pod
```

The CEO never delivers food directly.

Similarly, the Control Plane never runs your application.

It only manages the workers.

---

# Why Do We Need a Cluster?

Without Kubernetes:

```
Docker Container

↓

Server Crashes

↓

Application Down
```

With Kubernetes:

```
Container Dies

↓

kubelet Detects Failure

↓

ReplicaSet Creates New Pod

↓

Application Continues Running
```

Benefits:

- High Availability
- Auto Healing
- Load Balancing
- Auto Scaling
- Easy Updates
- Rollbacks
- Fault Tolerance

---

# Kubernetes Cluster Components

```
                 Kubernetes Cluster

        +-------------------------------+
        |        Control Plane          |
        +-------------------------------+
                    │
     ┌──────────────┼──────────────┐
     ▼              ▼              ▼
 API Server     Scheduler     Controller
                                    │
                                    ▼
                                  etcd
                    │
         -----------------------
         │                     │
         ▼                     ▼
    Worker Node 1        Worker Node 2
         │                     │
    kubelet              kubelet
    kube-proxy           kube-proxy
    containerd           containerd
         │                     │
       Pods                  Pods
```

---

# Control Plane

The **Control Plane** is the brain of Kubernetes.

It decides:

- Where Pods should run.
- Whether a Pod should be restarted.
- Which Node should receive a new Pod.
- Whether scaling is required.

It does **not** run your application containers.

### Main Components

| Component | Responsibility |
|-----------|----------------|
| API Server | Entry point for all Kubernetes requests |
| Scheduler | Chooses the best node for a Pod |
| Controller Manager | Ensures the desired state is maintained |
| etcd | Stores cluster configuration and state |
| Cloud Controller Manager | Integrates with cloud providers (AWS, Azure, GCP) |

---

# Worker Node

A **Worker Node** is the machine where your applications actually run.

Each Worker Node contains:

| Component | Purpose |
|-----------|---------|
| kubelet | Talks to the Control Plane and manages Pods |
| kube-proxy | Handles networking and Service traffic |
| Container Runtime | Runs containers (containerd, CRI-O, etc.) |
| Pods | Your applications |

```
Worker Node

+----------------------------------+
| kubelet                          |
| kube-proxy                       |
| containerd                       |
|                                  |
| Pod A                            |
| Pod B                            |
| Pod C                            |
+----------------------------------+
```

---

# How Kubernetes Works Internally

Suppose you run:

```bash
kubectl apply -f deployment.yaml
```

Internally:

```
kubectl

↓

API Server

↓

Authentication

↓

Authorization

↓

Admission Controllers

↓

etcd

↓

Scheduler

↓

Worker Node Selected

↓

kubelet

↓

containerd

↓

Pod Created
```

This flow is one of the most commonly asked Kubernetes interview topics.

---

# What is Minikube?

**Minikube** is a tool that lets you run a Kubernetes cluster on your local machine.

Instead of needing multiple servers, Minikube creates a **single-node Kubernetes cluster** for learning and development.

Think of it as a **mini Kubernetes cluster** on your laptop.

### Why Use Minikube?

- Learn Kubernetes locally
- Test YAML files
- Practice kubectl commands
- Experiment safely without cloud costs

---

# Minikube Architecture

```
                 Your Laptop

+------------------------------------------------+
|                Minikube                        |
|                                                |
|  +------------------------------------------+  |
|  | Kubernetes Cluster                       |  |
|  |                                          |  |
|  | Control Plane                            |  |
|  | kubelet                                 |  |
|  | kube-proxy                              |  |
|  | containerd                              |  |
|  | Pods                                    |  |
|  +------------------------------------------+  |
+------------------------------------------------+
```

Unlike a production cluster with many worker nodes, Minikube usually combines everything into a single node.

---

# Common Minikube Drivers

| Driver | Platform |
|---------|----------|
| Docker | Recommended for Windows/macOS/Linux |
| Hyper-V | Windows |
| VirtualBox | Cross-platform |
| KVM2 | Linux |
| HyperKit | macOS |

You used the Docker driver:

```bash
minikube start --driver=docker
```

---

# Frequently Used Minikube Commands

| Command | Purpose |
|---------|---------|
| `minikube start` | Start the cluster |
| `minikube start --driver=docker` | Start using Docker |
| `minikube status` | Check cluster status |
| `minikube stop` | Stop the cluster |
| `minikube delete` | Delete the cluster |
| `minikube ssh` | SSH into the node |
| `minikube ip` | Display cluster IP |
| `minikube dashboard` | Open Kubernetes Dashboard |
| `minikube service <service> --url` | Access a NodePort service |
| `minikube addons list` | Show available add-ons |
| `minikube addons enable ingress` | Enable Ingress controller |

---

# 🔥 Pro Tips

- Use `minikube status` before troubleshooting Kubernetes issues.
- Use `minikube ssh` to inspect the node directly.
- Prefer the Docker driver unless another driver is required.
- Enable only the add-ons you need to keep the cluster lightweight.

---

# ⚠️ Common Mistakes

❌ Running `kubectl` before starting Minikube.

```bash
kubectl get pods
```

Error:

```
The connection to the server localhost:8080 was refused
```

✔️ Fix:

```bash
minikube start
```

---

❌ Using the wrong flag:

```bash
minikube start --drive=docker
```

✔️ Correct:

```bash
minikube start --driver=docker
```

---

# 🎯 Interview Questions

### Beginner

1. What is a Kubernetes Cluster?
2. What is Minikube?
3. Why do we use Minikube?
4. Difference between a cluster and a node?
5. Can applications run directly on the Control Plane?

### Intermediate

6. Explain the architecture of a Kubernetes cluster.
7. What happens internally when you execute `kubectl apply`?
8. Why is etcd important?
9. What is the role of kubelet?
10. Why is the API Server called the gateway of Kubernetes?

---

# 📝 Quick Revision

- A Kubernetes Cluster consists of a **Control Plane** and one or more **Worker Nodes**.
- The Control Plane manages the cluster; Worker Nodes run applications.
- `kubectl` communicates with the API Server.
- `etcd` stores the cluster state.
- `Scheduler` assigns Pods to Nodes.
- `kubelet` ensures Pods are running on each node.
- `kube-proxy` handles networking.
- Minikube is a local, single-node Kubernetes cluster for learning and testing.
