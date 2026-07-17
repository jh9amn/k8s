# 📚 02 - Pods

> **Pods are the smallest deployable unit in Kubernetes.**
>
> Although containers run your applications, Kubernetes **never deploys containers directly**. Instead, it deploys Pods, which act as a wrapper around one or more containers.

---

# 📖 Table of Contents

- What is a Pod?
- Why Do We Need Pods?
- Pod Architecture
- Pod Lifecycle
- Types of Pods
- Pod Networking
- Creating Pods
- Pod Commands
- Debugging Pods
- Common Errors
- Interview Questions
- Quick Revision

---

# What is a Pod?

A **Pod** is the smallest object that Kubernetes can create and manage.

Think of a Pod as a **wrapper around one or more containers**.

```
+--------------------------------------+
|               Pod                    |
|                                      |
|  +-------------------------------+   |
|  |      Container (Nginx)         |   |
|  +-------------------------------+   |
|                                      |
+--------------------------------------+
```

A Pod contains:

- One or more containers
- Shared IP Address
- Shared Storage (Volumes)
- Shared Network Namespace

---

# Why Doesn't Kubernetes Deploy Containers Directly?

Imagine Kubernetes deployed only containers.

```
Container crashes

↓

No object exists to manage it
```

Instead:

```
Container

↓

Pod

↓

ReplicaSet

↓

Deployment
```

The Pod becomes the unit that Kubernetes can monitor, replace, restart, and scale.

---

# Real World Analogy

Imagine a delivery box.

```
Amazon Box

↓

Items Inside
```

The delivery company doesn't deliver individual items.

It delivers the **box**.

Similarly:

```
Pod

↓

Containers
```

Kubernetes schedules Pods, not containers.

---

# Pod Architecture

```
                Pod

+--------------------------------------+
|                                      |
|   IP Address                         |
|                                      |
|   Volume                             |
|                                      |
| +-------------+ +-------------+      |
| | Container A | | Container B |      |
| +-------------+ +-------------+      |
|                                      |
+--------------------------------------+
```

All containers inside a Pod share:

- Same IP
- Same localhost
- Same storage volumes

---

# Single Container Pod

The most common case.

```
Pod

↓

Nginx Container
```

---

# Multi-Container Pod

Sometimes one Pod contains multiple containers.

Example:

```
Pod

├── Main Application

└── Log Collector
```

Another example:

```
Pod

├── Application

└── Istio Sidecar Proxy
```

This pattern is called the **Sidecar Pattern**.

---

# Pod Lifecycle

```
Pending

↓

ContainerCreating

↓

Running

↓

Succeeded

↓

Failed
```

or

```
Pending

↓

Running

↓

CrashLoopBackOff
```

---

## Pending

Pod has been accepted but isn't running yet.

Possible reasons:

- Image pulling
- Scheduling
- Volume mounting

---

## Running

Everything is healthy.

Application is serving traffic.

---

## Succeeded

Container completed successfully.

Usually used by Jobs.

---

## Failed

Container exited with failure.

---

## Unknown

The control plane lost communication with the node.

---

# Pod Networking

Every Pod gets:

```
One Unique IP Address
```

Example:

```
Pod A

10.244.0.5

↓

Pod B

10.244.0.6
```

Pods communicate directly using their IP addresses.

However,

**Pod IPs are temporary.**

Never access Pods directly in production.

Use a **Service** instead.

---

# Creating a Pod

Example YAML

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:

  - name: nginx

    image: nginx:latest

    ports:

    - containerPort: 80
```

Create it

```bash
kubectl apply -f pod.yaml
```

---

# Pod Commands

## List Pods

```bash
kubectl get pods
```

---

## Watch Pods

```bash
kubectl get pods -w
```

---

## Detailed Output

```bash
kubectl get pods -o wide
```

Shows:

- Node
- Pod IP
- Internal IP

---

## Describe Pod

```bash
kubectl describe pod nginx-pod
```

Shows:

- Events
- Image
- Restart Count
- Conditions
- Mounted Volumes

---

## Pod Logs

```bash
kubectl logs nginx-pod
```

Follow logs

```bash
kubectl logs -f nginx-pod
```

---

## Execute Inside Pod

```bash
kubectl exec -it nginx-pod -- /bin/bash
```

If bash isn't available:

```bash
kubectl exec -it nginx-pod -- /bin/sh
```

---

## Delete Pod

```bash
kubectl delete pod nginx-pod
```

---

# What Happens Internally?

```
kubectl delete pod nginx

↓

API Server

↓

etcd Updated

↓

kubelet Receives Request

↓

Container Stops

↓

ReplicaSet Notices Missing Pod

↓

Creates New Pod

↓

Scheduler Selects Node

↓

kubelet Starts Container
```

If the Pod is managed by a Deployment, it will be recreated automatically.

---

# Debugging Flow

```
Pod Not Running

↓

kubectl get pods

↓

kubectl describe pod

↓

kubectl logs

↓

kubectl exec

↓

kubectl get events
```

---

# Common Errors

## CrashLoopBackOff

Meaning:

Application starts and immediately crashes.

Debug:

```bash
kubectl logs <pod-name>
```

---

## ImagePullBackOff

Meaning:

Image cannot be downloaded.

Possible causes:

- Wrong image name
- Image doesn't exist
- Private registry
- Authentication failure

---

## ErrImagePull

Check:

```bash
kubectl describe pod
```

---

## Pending

Possible reasons:

- Insufficient CPU
- Insufficient Memory
- PVC not bound
- Node unavailable

---

# Best Practices

✅ One application per Pod.

✅ Never create Pods directly in production.

✅ Use Deployments instead.

✅ Add resource requests and limits.

✅ Add readiness and liveness probes.

---

# 🔥 Pro Tips

- Use `kubectl get pods -o wide` to see the node and Pod IP.
- Start troubleshooting with `kubectl describe pod` before checking logs.
- Use labels on Pods so Services and Deployments can select them.

---

# ⚠️ Common Mistakes

❌ Accessing Pods directly by IP.

✔️ Use a Service because Pod IPs change.

---

❌ Creating Pods manually in production.

✔️ Use Deployments.

---

❌ Deleting Pods to "fix" applications.

✔️ Find the root cause with `kubectl describe` and `kubectl logs`.

---

# Interview Questions

### Beginner

1. What is a Pod?
2. Why is Pod the smallest deployable unit?
3. Can a Pod contain multiple containers?
4. What do containers inside the same Pod share?
5. What is the difference between a Pod and a container?

### Intermediate

6. Explain the Pod lifecycle.
7. What is CrashLoopBackOff?
8. What is ImagePullBackOff?
9. Why shouldn't Pod IPs be used directly?
10. Why are Pods considered ephemeral?

### Advanced

11. Explain the Sidecar pattern.
12. What happens internally when a Pod is deleted?
13. How does kubelet monitor Pods?
14. Why do Pods restart even after deletion when managed by a Deployment?

---

# Quick Revision

- Pod = Smallest deployable Kubernetes object.
- Pods wrap one or more containers.
- Containers inside a Pod share network and storage.
- Each Pod gets a unique IP.
- Pod IPs are temporary.
- Use Services for stable networking.
- Use Deployments instead of standalone Pods.
- `kubectl describe` and `kubectl logs` are the first debugging commands.
