# 📚 Chapter 13 - Kubernetes CI/CD, GitOps & Production Best Practices

> **Kubernetes is only one part of modern DevOps.**
>
> In production, code is built, tested, packaged, deployed, monitored, and continuously updated through automated pipelines.

---

# 📖 Table of Contents

1. DevOps Pipeline
2. CI/CD Overview
3. CI vs CD
4. Kubernetes Deployment Pipeline
5. GitOps
6. Argo CD
7. Flux CD
8. Jenkins + Kubernetes
9. GitHub Actions + Kubernetes
10. Deployment Strategies
11. Autoscaling
12. Resource Management
13. Production Checklist
14. Kubernetes Ecosystem
15. End-to-End Architecture
16. Interview Questions
17. Quick Revision

---

# What is CI/CD?

CI/CD automates software delivery.

```
Developer

↓

Write Code

↓

Git Push

↓

CI Pipeline

↓

Run Tests

↓

Build Docker Image

↓

Push Image

↓

Deploy to Kubernetes

↓

Monitor Application
```

---

# Continuous Integration (CI)

CI focuses on integrating code frequently.

Typical CI tasks:

- Compile code
- Run unit tests
- Static code analysis
- Build Docker image
- Push image to registry

Example tools:

- Jenkins
- GitHub Actions
- GitLab CI
- CircleCI

---

# Continuous Delivery (CD)

CD automates deployment preparation.

Pipeline:

```
Docker Image

↓

Deploy to Staging

↓

Approval (optional)

↓

Deploy to Production
```

Some teams automate the final production deployment (Continuous Deployment), while others require manual approval (Continuous Delivery).

---

# CI vs CD

| Continuous Integration | Continuous Delivery / Deployment |
|-------------------------|----------------------------------|
| Build & Test | Deploy Application |
| Produces artifacts | Releases artifacts |
| Detects code issues | Delivers updates safely |

---

# Kubernetes Deployment Pipeline

```
Developer

↓

GitHub

↓

GitHub Actions / Jenkins

↓

Docker Build

↓

Docker Hub / Registry

↓

Update Kubernetes Manifest

↓

Argo CD

↓

Kubernetes Cluster

↓

Prometheus + Grafana
```

---

# GitOps

GitOps means:

> **Git is the single source of truth.**

Instead of running:

```bash
kubectl apply -f deployment.yaml
```

You update Git:

```
deployment.yaml

↓

Git Commit

↓

Git Push
```

A GitOps tool notices the change and synchronizes the cluster automatically.

---

# GitOps Workflow

```
Git Repository

↓

Manifest Changed

↓

Argo CD / Flux

↓

Sync

↓

Kubernetes Cluster
```

Benefits:

- Version control
- Easy rollback
- Auditable changes
- Consistent environments

---

# Argo CD

Argo CD is a popular GitOps tool.

Responsibilities:

- Watches Git repositories
- Detects changes
- Synchronizes cluster state
- Supports rollback
- Shows deployment status in a web UI

```
Git

↓

Argo CD

↓

Cluster
```

---

# Flux CD

Flux is another GitOps solution.

Features:

- Pull-based synchronization
- Git-driven deployments
- Lightweight architecture
- Kubernetes-native

---

# Argo CD vs Flux

| Argo CD | Flux |
|----------|------|
| Rich web UI | Minimal UI |
| Easy to start | Lightweight |
| Strong application dashboard | Strong Git integration |

Both implement GitOps principles.

---

# Jenkins + Kubernetes

Pipeline:

```
Developer

↓

GitHub

↓

Jenkins

↓

Run Tests

↓

Build Docker Image

↓

Push Registry

↓

Deploy Kubernetes
```

Jenkins can also run build agents inside Kubernetes Pods.

---

# GitHub Actions + Kubernetes

Example flow:

```
Push Code

↓

GitHub Actions

↓

Docker Build

↓

Docker Push

↓

kubectl apply
```

Or:

```
GitHub Actions

↓

Update GitOps Repository

↓

Argo CD Deploys
```

---

# Deployment Strategies

## Recreate

```
Old Version

↓

Stop

↓

New Version
```

Simple but causes downtime.

---

## Rolling Update (Default)

```
Old Pods

↓

Replace One by One

↓

New Pods
```

Advantages:

- No downtime
- Safe rollout

---

## Blue-Green Deployment

```
Blue

(Current)

Green

(New)

↓

Switch Traffic
```

Rollback is quick by switching traffic back.

---

## Canary Deployment

```
10%

↓

30%

↓

60%

↓

100%
```

A small percentage of users receive the new version first.

Useful for reducing deployment risk.

---

# Horizontal Pod Autoscaler (HPA)

Automatically changes the number of Pod replicas.

```
CPU High

↓

More Pods

↓

Load Distributed
```

Example:

```bash
kubectl autoscale deployment my-app \
--cpu-percent=70 \
--min=2 \
--max=10
```

---

# Vertical Pod Autoscaler (VPA)

Instead of adding Pods:

```
Increase CPU

Increase Memory
```

Resources per Pod are adjusted based on usage.

---

# Cluster Autoscaler

Adds or removes worker nodes.

```
No Capacity

↓

Add Node

↓

Schedule Pods
```

Supported by many cloud providers.

---

# Resource Quotas

Limit resource consumption within a namespace.

Example:

```
Namespace

↓

Maximum Pods

Maximum CPU

Maximum Memory
```

Helps prevent one team from consuming all cluster resources.

---

# LimitRange

Sets default and maximum resource requests/limits for containers in a namespace.

Example:

```
Default CPU

↓

500m
```

Developers don't have to specify it every time.

---

# Production Checklist

## Security

✅ RBAC

✅ Network Policies

✅ Secrets management

✅ Non-root containers

---

## Reliability

✅ Liveness Probes

✅ Readiness Probes

✅ Startup Probes

---

## Resources

✅ CPU Requests

✅ Memory Requests

✅ CPU Limits

✅ Memory Limits

---

## Monitoring

✅ Prometheus

✅ Grafana

✅ Alerts

✅ Centralized Logging

---

## Deployment

✅ Helm

✅ GitOps

✅ Rolling Updates

✅ Rollback Plan

---

## Backup

✅ etcd backups

✅ Persistent Volume backups

---

# Kubernetes Ecosystem

Common production tools:

| Tool | Purpose |
|------|----------|
| Helm | Package management |
| Prometheus | Metrics collection |
| Grafana | Dashboards |
| Alertmanager | Alert routing |
| Argo CD | GitOps deployment |
| Flux CD | GitOps deployment |
| Istio | Service mesh |
| Cilium | Networking & security |
| cert-manager | TLS certificate automation |
| ExternalDNS | DNS automation |
| Velero | Backup & disaster recovery |
| KEDA | Event-driven autoscaling |
| NGINX Ingress Controller | HTTP/HTTPS ingress |

---

# End-to-End Production Architecture

```
Developer

↓

GitHub

↓

GitHub Actions / Jenkins

↓

Docker Build

↓

Container Registry

↓

GitOps Repository

↓

Argo CD

↓

Kubernetes Cluster

↓

Ingress

↓

Service

↓

Pods

↓

Persistent Volume

↓

Monitoring

↓

Prometheus

↓

Grafana

↓

Alerts

↓

Operations Team
```

---

# Real Production Flow

```
Code Change

↓

Pull Request

↓

Merge

↓

CI Pipeline

↓

Tests Pass

↓

Docker Build

↓

Image Registry

↓

Git Manifest Updated

↓

Argo CD Sync

↓

Rolling Update

↓

Prometheus Monitors

↓

Grafana Displays Metrics

↓

Alertmanager Sends Alerts (if needed)
```

---

# Best Practices

✅ Keep Git as the source of truth.

✅ Automate builds and deployments.

✅ Prefer Rolling Updates for most applications.

✅ Use Canary or Blue-Green for critical services.

✅ Monitor everything.

✅ Regularly test backups and disaster recovery.

✅ Keep Kubernetes and dependencies updated.

---

# Common Mistakes

❌ Deploying directly to production without testing.

❌ Making manual changes in the cluster that are not reflected in Git.

❌ Not configuring resource requests/limits.

❌ Ignoring monitoring and alerts.

❌ Running without a rollback strategy.

---

# Interview Questions

## Beginner

1. What is CI/CD?
2. Difference between CI and CD?
3. What is GitOps?
4. What is Argo CD?
5. What is HPA?

---

## Intermediate

6. Difference between Argo CD and Flux?
7. Explain Rolling Update.
8. Explain Blue-Green Deployment.
9. Explain Canary Deployment.
10. Difference between HPA and VPA?

---

## Advanced

11. Design a production Kubernetes deployment pipeline.
12. How would you implement GitOps?
13. How would you secure a CI/CD pipeline?
14. How would you deploy with zero downtime?
15. How would you recover from a failed production deployment?

---

# 📝 Quick Revision

- CI builds and tests code.
- CD delivers applications safely.
- GitOps uses Git as the source of truth.
- Argo CD and Flux continuously synchronize clusters with Git.
- Rolling Update is the default Kubernetes deployment strategy.
- Blue-Green enables near-instant rollback.
- Canary gradually exposes new versions to users.
- HPA scales Pods horizontally.
- VPA adjusts CPU and memory per Pod.
- Cluster Autoscaler scales worker nodes.
- ResourceQuotas and LimitRanges control resource usage.
- Production success depends on automation, observability, security, and repeatable deployments.
