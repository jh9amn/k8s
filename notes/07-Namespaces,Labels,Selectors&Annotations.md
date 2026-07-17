# 📚 07 - Namespaces, Labels, Selectors & Annotations

> Kubernetes can run thousands of applications inside one cluster.
> Namespaces, Labels, Selectors, and Annotations help organize, identify, and manage these resources efficiently.

---

# 📖 Table of Contents

1. Why Resource Organization is Important
2. Namespaces
3. Default Namespaces
4. Namespace Architecture
5. Namespace Commands
6. Labels
7. Label Selectors
8. MatchLabels vs MatchExpressions
9. Annotations
10. Labels vs Annotations
11. ResourceQuota
12. LimitRange
13. Best Practices
14. Common Errors
15. Interview Questions
16. Quick Revision

---

# Why Resource Organization Matters

Imagine a company with three teams.

```
Company

├── Development
├── Testing
└── Production
```

Each team deploys:

- Pods
- Services
- Deployments
- ConfigMaps
- Secrets

Without organization:

```
Cluster

├── 200 Pods
├── 150 Services
├── 75 Deployments

Everything mixed together ❌
```

Finding resources becomes difficult.

Kubernetes solves this using **Namespaces**.

---

# What is a Namespace?

A **Namespace** is a logical partition inside a Kubernetes cluster.

Think of it as a folder that groups related resources.

```
Kubernetes Cluster

├── Namespace: development
│      ├── Pods
│      ├── Services
│      └── Deployments
│
├── Namespace: testing
│      ├── Pods
│      ├── Services
│      └── Deployments
│
└── Namespace: production
       ├── Pods
       ├── Services
       └── Deployments
```

Namespaces **do not create another cluster**.

They simply organize resources inside one cluster.

---

# Why Use Namespaces?

Benefits:

- Resource isolation
- Better organization
- Team separation
- RBAC control
- Resource quotas
- Environment separation

Example:

```
Cluster

├── dev
├── qa
├── staging
└── production
```

Each environment can have its own resources without name conflicts.

---

# Default Namespaces

Every Kubernetes cluster includes several built-in namespaces.

| Namespace | Purpose |
|------------|---------|
| default | User applications (default if none specified) |
| kube-system | Kubernetes system components |
| kube-public | Publicly readable resources |
| kube-node-lease | Node heartbeat information |

View them:

```bash
kubectl get namespaces
```

or

```bash
kubectl get ns
```

---

# Namespace Architecture

```
                 Kubernetes Cluster

        ┌─────────────────────────────┐
        │                             │
        │ Namespace: dev              │
        │   ├── Pods                  │
        │   ├── Services              │
        │   └── Deployments           │
        │                             │
        │ Namespace: prod             │
        │   ├── Pods                  │
        │   ├── Services              │
        │   └── Deployments           │
        └─────────────────────────────┘
```

Resources with the same name can exist in different namespaces.

Example:

```
dev/nginx

prod/nginx
```

No conflict occurs because the namespace is part of the resource identity.

---

# Namespace Commands

## Create Namespace

```bash
kubectl create namespace dev
```

---

## List Namespaces

```bash
kubectl get ns
```

---

## Delete Namespace

```bash
kubectl delete namespace dev
```

Deleting a namespace deletes **all namespaced resources** inside it.

---

## Create Resources in a Namespace

```bash
kubectl apply -f deployment.yaml -n dev
```

---

## View Resources in a Namespace

```bash
kubectl get pods -n dev
```

---

## Set Current Namespace (Context)

```bash
kubectl config set-context --current --namespace=dev
```

---

# What are Labels?

A **Label** is a key-value pair attached to a Kubernetes object.

Example:

```yaml
labels:
  app: nginx
  env: production
  tier: frontend
```

Labels are used to **identify and group resources**.

Think of them like tags on files.

---

# Why Labels Matter

Suppose you have:

```
Pod 1

app=nginx

env=prod
```

```
Pod 2

app=nginx

env=dev
```

```
Pod 3

app=redis

env=prod
```

You can easily filter:

```
All nginx Pods

↓

app=nginx
```

or

```
Production Pods

↓

env=prod
```

---

# Label Commands

Show labels:

```bash
kubectl get pods --show-labels
```

Add a label:

```bash
kubectl label pod nginx app=frontend
```

Remove a label:

```bash
kubectl label pod nginx app-
```

---

# What is a Selector?

A **Selector** tells Kubernetes which resources should be matched.

Services, ReplicaSets, Deployments, and many controllers rely on selectors.

Example:

```yaml
selector:
  app: nginx
```

This Service forwards traffic only to Pods with:

```yaml
labels:
  app: nginx
```

---

# matchLabels

Simple equality matching.

```yaml
selector:
  matchLabels:
    app: nginx
    env: production
```

Only Pods matching both labels are selected.

---

# matchExpressions

More advanced matching.

Example:

```yaml
matchExpressions:
- key: env
  operator: In
  values:
    - production
    - staging
```

Common operators:

- In
- NotIn
- Exists
- DoesNotExist

---

# Labels vs Selectors

```
Pod

↓

Label

↓

app=nginx
```

```
Service

↓

Selector

↓

app=nginx
```

If the selector matches the label, traffic is routed to the Pod.

---

# What are Annotations?

Annotations are also key-value pairs, but they are **not used for selecting resources**.

They store metadata.

Example:

```yaml
annotations:
  owner: aman
  description: Production API
  documentation: https://company/wiki
```

Use cases:

- Build information
- Deployment timestamps
- Commit SHA
- Documentation links
- Tool-specific metadata

---

# Labels vs Annotations

| Labels | Annotations |
|---------|-------------|
| Identify objects | Store metadata |
| Used by selectors | Ignored by selectors |
| Small, queryable values | Larger descriptive values |
| Used for grouping | Used for documentation and tooling |

---

# ResourceQuota

A **ResourceQuota** limits resource usage within a namespace.

Example:

```yaml
apiVersion: v1
kind: ResourceQuota

spec:
  hard:
    pods: "20"
    requests.cpu: "10"
    requests.memory: 20Gi
```

Benefits:

- Prevent one team from consuming all cluster resources.
- Fair resource sharing.

---

# LimitRange

A **LimitRange** sets default or maximum/minimum resource requests and limits for containers in a namespace.

Example:

```yaml
apiVersion: v1
kind: LimitRange

spec:
  limits:
  - default:
      cpu: "500m"
      memory: "512Mi"
```

Benefits:

- Enforces sensible defaults.
- Prevents containers from requesting excessive resources.

---

# Best Practices

✅ Use namespaces for environments or teams.

✅ Use meaningful labels.

Example:

```yaml
app: payment
env: production
version: v2
tier: backend
```

✅ Keep labels consistent across resources.

✅ Use annotations for descriptive metadata.

---

# Common Errors

## Service Has No Endpoints

Reason:

Service selector doesn't match Pod labels.

Check:

```bash
kubectl get pods --show-labels
kubectl describe svc
```

---

## Resource Not Found

You may be looking in the wrong namespace.

Check:

```bash
kubectl get pods -A
```

or

```bash
kubectl get pods -n <namespace>
```

---

## Quota Exceeded

Reason:

Namespace reached its ResourceQuota.

Check:

```bash
kubectl describe resourcequota -n <namespace>
```

---

# Useful Commands

```bash
kubectl get ns
```

```bash
kubectl get pods -A
```

```bash
kubectl get pods -n dev
```

```bash
kubectl get pods --show-labels
```

```bash
kubectl label pod nginx env=dev
```

```bash
kubectl describe namespace dev
```

```bash
kubectl describe quota -n dev
```

```bash
kubectl describe limitrange -n dev
```

---

# Interview Questions

## Beginner

1. What is a Namespace?
2. Why do we need Namespaces?
3. What are Labels?
4. What are Selectors?
5. What are Annotations?

---

## Intermediate

6. Difference between Labels and Annotations?
7. Explain matchLabels vs matchExpressions.
8. Why are selectors important?
9. What is ResourceQuota?
10. What is LimitRange?

---

## Advanced

11. Can two Pods with the same name exist in a cluster?
12. How does Kubernetes use labels internally?
13. Why shouldn't frequently changing data be stored in labels?
14. How would you organize namespaces in a production cluster?

---

# 📝 Quick Revision

- Namespace = Logical partition inside a cluster.
- Labels = Key-value pairs used to identify and group resources.
- Selectors = Match labels to choose resources.
- Annotations = Additional metadata, not used for selection.
- ResourceQuota = Limits total resource usage in a namespace.
- LimitRange = Sets default and maximum/minimum resource requests and limits.
- Services, ReplicaSets, and Deployments all rely on labels and selectors.
