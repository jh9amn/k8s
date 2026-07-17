# 📚 09 - Kubernetes Scheduling, Node Selection, Taints & Affinity

> **The Kubernetes Scheduler decides where Pods run.**
>
> It evaluates available worker nodes and selects the most suitable one based on resources, policies, constraints, and scheduling rules.

---

# 📖 Table of Contents

1. What is Scheduling?
2. Kubernetes Scheduler
3. Scheduling Workflow
4. Node Selector
5. Node Affinity
6. Pod Affinity
7. Pod Anti-Affinity
8. Taints
9. Tolerations
10. Taints vs Affinity
11. Resource Requests & Limits
12. Quality of Service (QoS)
13. Cordon, Drain & Uncordon
14. Scheduling Debugging
15. Useful Commands
16. Best Practices
17. Interview Questions
18. Quick Revision

---

# Why Scheduling Matters?

Suppose your cluster has three worker nodes.

```
                Cluster

        ┌────────┬────────┬────────┐
        │ Node 1 │ Node 2 │ Node 3 │
        └────────┴────────┴────────┘
```

A new Pod is created.

Question:

```
Which node should run it?
```

This decision is made by the **Kubernetes Scheduler**.

---

# What is the Kubernetes Scheduler?

The **kube-scheduler** is a control plane component that watches for newly created Pods that do not yet have a node assigned.

Its responsibilities:

- Find eligible nodes.
- Filter unsuitable nodes.
- Score the remaining nodes.
- Select the best node.
- Bind the Pod to that node.

---

# Scheduling Workflow

```
Deployment

↓

Pod Created

↓

API Server

↓

Scheduler

↓

Find Candidate Nodes

↓

Score Nodes

↓

Select Best Node

↓

Bind Pod

↓

Kubelet Starts Container
```

---

# Scheduler Filtering

The scheduler first removes nodes that cannot run the Pod.

Reasons a node may be rejected:

- Not enough CPU
- Not enough Memory
- Taints not tolerated
- Node Selector mismatch
- Node Affinity mismatch
- Volume constraints

Example:

```
Node A

CPU Available = 2

Memory = 4 GB

✔ Eligible

---------------------

Node B

CPU Available = 0

✖ Rejected
```

---

# Scheduler Scoring

If multiple nodes are eligible, Kubernetes scores them.

It prefers nodes based on factors such as:

- Better resource balance
- Affinity preferences
- Topology spread
- Image locality (when applicable)

Highest score wins.

---

# Node Selector

The simplest way to control scheduling.

Label a node:

```bash
kubectl label node worker-1 disktype=ssd
```

Deployment:

```yaml
spec:
  nodeSelector:
    disktype: ssd
```

Only nodes with:

```
disktype=ssd
```

can run the Pod.

---

# Viewing Node Labels

```bash
kubectl get nodes --show-labels
```

---

# Node Affinity

Node Affinity is more powerful than `nodeSelector`.

Example:

```yaml
affinity:

  nodeAffinity:

    requiredDuringSchedulingIgnoredDuringExecution:

      nodeSelectorTerms:

      - matchExpressions:

        - key: disktype

          operator: In

          values:

          - ssd
```

---

# Types of Node Affinity

### Required

```
Must match

↓

Otherwise Pod won't run
```

Field:

```
requiredDuringSchedulingIgnoredDuringExecution
```

---

### Preferred

```
Prefer this node

↓

Can still schedule elsewhere if needed
```

Field:

```
preferredDuringSchedulingIgnoredDuringExecution
```

---

# Node Selector vs Node Affinity

| Node Selector | Node Affinity |
|---------------|---------------|
| Simple | Flexible |
| Exact match only | Multiple operators |
| Basic filtering | Advanced scheduling |
| Less expressive | More expressive |

---

# Pod Affinity

Schedule Pods close to other Pods.

Example:

```
Frontend

↓

Run Near

↓

Backend
```

Reason:

Lower network latency.

---

# Pod Anti-Affinity

Do the opposite.

```
Pod A

↓

Different Node

↓

Pod B
```

Useful for:

- High Availability
- Fault Tolerance
- Load Distribution

Example:

Never place two replicas of the same application on the same node.

---

# Taints

Taints keep Pods **away** from nodes unless they tolerate the taint.

Think:

```
Node

↓

Do Not Enter
```

Create:

```bash
kubectl taint nodes worker-1 dedicated=db:NoSchedule
```

Meaning:

```
Only Pods that tolerate this taint
can be scheduled here.
```

---

# Taint Effects

## NoSchedule

```
New Pods

↓

Cannot Schedule
```

---

## PreferNoSchedule

Scheduler tries to avoid the node, but may still place Pods there if necessary.

---

## NoExecute

Existing Pods without a matching toleration are evicted, and new Pods won't be scheduled.

---

# Tolerations

A toleration allows a Pod to be scheduled onto a tainted node.

Example:

```yaml
tolerations:

- key: "dedicated"

  operator: "Equal"

  value: "db"

  effect: "NoSchedule"
```

Now the Pod can run on:

```
dedicated=db
```

---

# Taints vs Tolerations

```
Node

↓

Taint

↓

"No Entry"

↓

Pod

↓

Toleration

↓

"I Have Permission"

↓

Scheduling Allowed
```

---

# Taints vs Node Affinity

| Taints | Node Affinity |
|---------|---------------|
| Repel Pods | Attract Pods |
| Applied to Nodes | Defined in Pods |
| Restricts scheduling | Expresses placement preference/requirement |

Often they are used together.

---

# Resource Requests

A request is the minimum amount of resources the scheduler reserves.

Example:

```yaml
resources:

  requests:

    cpu: "500m"

    memory: "512Mi"
```

Scheduler uses requests when deciding where a Pod can fit.

---

# Resource Limits

Maximum resources a container may use.

```yaml
resources:

  limits:

    cpu: "1"

    memory: "1Gi"
```

If memory exceeds the limit, the container can be terminated (OOMKilled).

---

# Requests vs Limits

| Requests | Limits |
|-----------|---------|
| Used by scheduler | Runtime maximum |
| Minimum guaranteed | Maximum allowed |
| Affects placement | Affects execution |

---

# Quality of Service (QoS)

Kubernetes assigns a QoS class to every Pod.

## Guaranteed

Requests == Limits for CPU and Memory.

Highest priority during resource pressure.

---

## Burstable

Requests are set, but limits differ (or only some resources have requests/limits).

Most common.

---

## BestEffort

No requests.

No limits.

Lowest priority.

First to be evicted when the node runs out of resources.

---

# Cordon

Prevent new Pods from scheduling onto a node.

```bash
kubectl cordon worker-1
```

Existing Pods continue running.

---

# Drain

Safely evict workloads from a node before maintenance.

```bash
kubectl drain worker-1 --ignore-daemonsets
```

Use before upgrading or shutting down a node.

---

# Uncordon

Allow scheduling again.

```bash
kubectl uncordon worker-1
```

---

# Scheduling Debugging

Pod Pending?

Check:

```bash
kubectl describe pod <pod-name>
```

Look at:

```
Events
```

Typical messages:

```
0/3 nodes available

Insufficient CPU

Node Affinity Mismatch

Untolerated Taint
```

---

# Useful Commands

View Nodes

```bash
kubectl get nodes
```

---

View Node Labels

```bash
kubectl get nodes --show-labels
```

---

Describe Node

```bash
kubectl describe node worker-1
```

---

Label Node

```bash
kubectl label node worker-1 disktype=ssd
```

---

Remove Label

```bash
kubectl label node worker-1 disktype-
```

---

Add Taint

```bash
kubectl taint node worker-1 dedicated=db:NoSchedule
```

---

Remove Taint

```bash
kubectl taint node worker-1 dedicated=db:NoSchedule-
```

---

View Events

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

---

# Production Example

```
Database Node

↓

Taint

↓

Only Database Pods
```

```
GPU Node

↓

Label

↓

gpu=true

↓

Node Affinity

↓

AI Pods
```

```
Web Servers

↓

Pod Anti-Affinity

↓

Spread Across Nodes
```

This improves performance, reliability, and fault tolerance.

---

# Best Practices

✅ Set resource requests and limits.

✅ Prefer Node Affinity over `nodeSelector` for complex rules.

✅ Use Pod Anti-Affinity for high availability.

✅ Use taints to reserve specialized nodes.

✅ Drain nodes before maintenance.

---

# Common Mistakes

❌ Forgetting a toleration for a tainted node.

❌ Using only `nodeSelector` for complex placement logic.

❌ Setting requests larger than any node can satisfy.

❌ Not configuring resource requests, leading to poor scheduling decisions.

---

# Interview Questions

## Beginner

1. What is the Kubernetes Scheduler?
2. What is scheduling?
3. What is a Node Selector?
4. What is Node Affinity?
5. What are Taints?

---

## Intermediate

6. Difference between Node Selector and Node Affinity?
7. Difference between Taints and Tolerations?
8. Difference between Pod Affinity and Anti-Affinity?
9. What are resource requests and limits?
10. What happens when a Pod is Pending?

---

## Advanced

11. Explain the scheduler's filtering and scoring phases.
12. Explain QoS classes.
13. What is the difference between `NoSchedule`, `PreferNoSchedule`, and `NoExecute`?
14. When would you use `cordon` vs `drain`?
15. How do topology spread constraints complement Pod Anti-Affinity?

---

# 📝 Quick Revision

- kube-scheduler assigns Pods to nodes.
- `nodeSelector` provides simple node matching.
- Node Affinity provides advanced node selection.
- Pod Affinity keeps related Pods together.
- Pod Anti-Affinity spreads Pods apart.
- Taints repel Pods; tolerations allow exceptions.
- Resource requests influence scheduling; limits restrict runtime usage.
- QoS classes determine eviction priority.
- `cordon` blocks new scheduling.
- `drain` safely evicts Pods for maintenance.
- `uncordon` re-enables scheduling.
