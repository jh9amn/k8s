# 📚 03 - ReplicaSets & Deployments

> **Pods are temporary (ephemeral).** If a Pod crashes or is deleted, Kubernetes needs a way to ensure the desired number of Pods are always running. This is where **ReplicaSets** and **Deployments** come in.

---

# 📖 Table of Contents

- Why Pods Are Not Enough
- What is a ReplicaSet?
- Why Do We Need ReplicaSets?
- ReplicaSet Architecture
- ReplicaSet Commands
- What is a Deployment?
- Deployment Architecture
- Deployment vs ReplicaSet
- Rolling Updates
- Rollbacks
- Scaling Applications
- Self-Healing
- Deployment Strategies
- Common Errors
- Best Practices
- Interview Questions
- Quick Revision

---

# Why Pods Are Not Enough

Imagine you create a Pod directly.

```bash
kubectl apply -f pod.yaml
```

```
        Pod
         │
         ▼
     Running
         │
         ▼
      Pod Crashes
         │
         ▼
      Application Down ❌
```

Kubernetes **does not automatically recreate standalone Pods**.

Now imagine a ReplicaSet manages the Pod.

```
ReplicaSet
     │
     ▼
   Pod Running
     │
     ▼
  Pod Deleted
     │
     ▼
ReplicaSet Detects Missing Pod
     │
     ▼
Creates New Pod ✅
```

---

# What is a ReplicaSet?

A **ReplicaSet** ensures that a specified number of Pod replicas are always running.

If one Pod fails, the ReplicaSet immediately creates a replacement.

Example:

Desired replicas = **3**

```
ReplicaSet

     │

     ├────────► Pod 1

     ├────────► Pod 2

     └────────► Pod 3
```

Delete one Pod:

```
ReplicaSet

Pod 2 Deleted ❌

↓

ReplicaSet Detects Change

↓

Creates New Pod

↓

Again 3 Pods Running ✅
```

---

# Why Do We Need ReplicaSets?

Without ReplicaSet:

```
Pod Deleted

↓

Application Down
```

With ReplicaSet:

```
Pod Deleted

↓

ReplicaSet

↓

Creates New Pod

↓

Application Continues Running
```

Benefits:

- Self-Healing
- High Availability
- Desired State Management
- Automatic Pod Recreation

---

# ReplicaSet Architecture

```
                 ReplicaSet

                     │

         Desired Replicas = 3

                     │

       ┌─────────────┼─────────────┐

       ▼             ▼             ▼

     Pod 1         Pod 2         Pod 3
```

ReplicaSet constantly compares:

```
Desired State

vs

Current State
```

If they differ, it fixes the cluster.

---

# ReplicaSet YAML

```yaml
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: nginx-rs

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

Create it:

```bash
kubectl apply -f replicaset.yaml
```

---

# ReplicaSet Commands

## List ReplicaSets

```bash
kubectl get rs
```

---

## Describe ReplicaSet

```bash
kubectl describe rs nginx-rs
```

---

## Delete ReplicaSet

```bash
kubectl delete rs nginx-rs
```

---

## Scale ReplicaSet

```bash
kubectl scale rs nginx-rs --replicas=5
```

---

## Watch ReplicaSets

```bash
kubectl get rs -w
```

---

# What is a Deployment?

A **Deployment** is a higher-level Kubernetes object that manages ReplicaSets.

Think of it like this:

```
Deployment

↓

ReplicaSet

↓

Pods
```

You almost never create ReplicaSets manually in production.

Instead, you create Deployments.

---

# Deployment Architecture

```
                 Deployment

                      │

              Creates ReplicaSet

                      │

             Desired Replicas = 3

                      │

        ┌─────────────┼─────────────┐

        ▼             ▼             ▼

      Pod 1         Pod 2         Pod 3
```

Hierarchy:

```
Deployment

↓

ReplicaSet

↓

Pods

↓

Containers
```

---

# Deployment YAML

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

        image: nginx:latest

        ports:
        - containerPort: 80
```

Deploy:

```bash
kubectl apply -f deployment.yaml
```

---

# Deployment Commands

## List Deployments

```bash
kubectl get deploy
```

---

## Describe Deployment

```bash
kubectl describe deployment nginx-deployment
```

---

## Scale Deployment

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

---

## Restart Deployment

```bash
kubectl rollout restart deployment nginx-deployment
```

---

## Rollout Status

```bash
kubectl rollout status deployment nginx-deployment
```

---

## Rollout History

```bash
kubectl rollout history deployment nginx-deployment
```

---

## Rollback

```bash
kubectl rollout undo deployment nginx-deployment
```

---

# Rolling Update

Suppose:

Version 1

```
Pod v1
Pod v1
Pod v1
```

Deploy Version 2.

Deployment performs:

```
Delete One Pod

↓

Create One New Pod

↓

Repeat
```

Result:

```
Pod v2

Pod v2

Pod v2
```

Users experience **zero downtime**.

---

# Rollback

Suppose Version 2 has a bug.

```
Deployment

↓

Rollback

↓

ReplicaSet (Old Version)

↓

Pods Restored
```

Command:

```bash
kubectl rollout undo deployment nginx-deployment
```

---

# Scaling

Current:

```
3 Pods
```

Command:

```bash
kubectl scale deployment nginx-deployment --replicas=6
```

Result:

```
6 Running Pods
```

Scale Down:

```bash
kubectl scale deployment nginx-deployment --replicas=2
```

---

# Self-Healing

Delete a Pod.

```
kubectl delete pod nginx-abc123
```

Internally:

```
Pod Deleted

↓

ReplicaSet Notices

↓

New Pod Created

↓

Desired State Restored
```

---

# What Happens Internally?

```
kubectl apply -f deployment.yaml

↓

API Server

↓

etcd

↓

Deployment Created

↓

Deployment Creates ReplicaSet

↓

ReplicaSet Creates Pods

↓

Scheduler Selects Node

↓

kubelet Starts Containers
```

---

# Deployment Strategies

## Rolling Update ✅

```
Old Pod

↓

New Pod

↓

Old Pod Removed
```

Advantages:

- Zero downtime
- Default strategy
- Safe updates

---

## Recreate

```
Delete All Old Pods

↓

Create New Pods
```

Downtime occurs.

---

## Blue-Green Deployment

```
Blue Environment (Current)

↓

Green Environment (New)

↓

Switch Traffic
```

Used for production releases.

---

## Canary Deployment

```
5%

↓

20%

↓

50%

↓

100%
```

Gradually shifts traffic to the new version.

---

# Common Errors

## ImagePullBackOff

Wrong image.

Fix:

```bash
kubectl describe pod
```

---

## CrashLoopBackOff

Application crashes repeatedly.

Check:

```bash
kubectl logs
```

---

## Pods Not Scaling

Possible reasons:

- Resource shortage
- Node unavailable
- Incorrect selector

---

# Best Practices

✅ Always use Deployments for stateless applications.

✅ Avoid managing ReplicaSets directly.

✅ Use labels consistently.

✅ Monitor rollout status after updates.

✅ Use readiness probes for production deployments.

---

# 🔥 Pro Tips

- `kubectl rollout status` is the quickest way to confirm a deployment completed successfully.
- Use `kubectl rollout history` before performing a rollback so you know which revisions are available.
- Prefer Deployments over ReplicaSets unless you're learning or debugging.

---

# ⚠️ Common Mistakes

❌ Creating ReplicaSets manually in production.

✔️ Create Deployments instead.

---

❌ Deleting Pods to restart an application.

✔️ Use:

```bash
kubectl rollout restart deployment <deployment-name>
```

---

❌ Forgetting to update labels and selectors.

✔️ Ensure the Deployment selector matches the labels in the Pod template.

---

# Interview Questions

### Beginner

1. What is a ReplicaSet?
2. Why do we need Deployments?
3. Difference between Pod and ReplicaSet?
4. Difference between ReplicaSet and Deployment?
5. What is scaling?

### Intermediate

6. Explain Rolling Updates.
7. Explain Rollbacks.
8. What happens when a Pod is deleted?
9. What is the desired state?
10. Why are Deployments preferred over ReplicaSets?

### Advanced

11. Explain the Deployment controller.
12. How does Kubernetes achieve zero downtime?
13. Compare Rolling Update, Blue-Green, and Canary deployments.
14. How do labels and selectors affect Deployments?

---

# 📝 Quick Revision

- **ReplicaSet** maintains the desired number of Pods.
- **Deployment** manages ReplicaSets and provides updates, rollbacks, and scaling.
- Deployments are the standard way to deploy stateless applications.
- Rolling Updates replace Pods gradually, reducing downtime.
- Rollbacks restore a previous application version.
- Self-healing happens because the ReplicaSet continuously reconciles the desired state with the actual state.
