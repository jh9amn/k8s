# 📚 Chapter 12 - Helm: The Kubernetes Package Manager

> **Helm is to Kubernetes what apt is to Ubuntu or npm is to Node.js.**
>
> Instead of manually applying dozens of YAML files, Helm lets you install, upgrade, rollback, and manage Kubernetes applications using reusable packages called **Charts**.

---

# 📖 Table of Contents

1. What is Helm?
2. Why Helm?
3. Helm Architecture
4. Helm Components
5. Helm Chart Structure
6. Templates
7. Values.yaml
8. Helm Repository
9. Helm Release
10. Helm Lifecycle
11. Creating Your Own Chart
12. Production Best Practices
13. Useful Commands
14. Interview Questions
15. Quick Revision

---

# What is Helm?

Helm is the official package manager for Kubernetes.

Without Helm:

```
deployment.yaml
service.yaml
configmap.yaml
secret.yaml
ingress.yaml
pvc.yaml

kubectl apply -f ...
kubectl apply -f ...
kubectl apply -f ...
```

With Helm:

```bash
helm install my-app chart-name
```

Helm creates all Kubernetes resources automatically.

---

# Why Helm?

Imagine installing Prometheus manually.

You would need:

- Deployment
- Service
- ConfigMap
- Secret
- RBAC
- ServiceAccount
- PVC
- ClusterRole
- ClusterRoleBinding
- Ingress

That's dozens of YAML files.

Instead:

```bash
helm install prometheus prometheus-community/prometheus
```

Everything is installed automatically.

---

# Helm Architecture

```
            Helm CLI

                │

                ▼

          Kubernetes API

                │

                ▼

        Kubernetes Cluster

                │

                ▼

     Creates Kubernetes Resources
```

---

# Important Helm Concepts

## Chart

A package containing Kubernetes manifests.

Think:

```
Chart

↓

Application Template
```

---

## Repository

A collection of Helm Charts.

Example:

```
https://prometheus-community.github.io/helm-charts
```

---

## Release

A running instance of a Chart.

Example:

```
Chart

↓

Install

↓

Release
```

You can install the same chart multiple times using different release names.

---

# Helm Workflow

```
Add Repository

↓

Search Chart

↓

Install Chart

↓

Upgrade

↓

Rollback

↓

Uninstall
```

---

# Helm Chart Structure

```
mychart/

│

├── Chart.yaml

├── values.yaml

├── charts/

├── templates/

│     deployment.yaml

│     service.yaml

│     ingress.yaml

│     configmap.yaml

│     secret.yaml

│

└── .helmignore
```

---

# Chart.yaml

Contains chart metadata.

Example:

```yaml
apiVersion: v2

name: myapp

version: 1.0.0

description: My first Helm Chart
```

---

# values.yaml

Stores configurable values.

Example:

```yaml
replicaCount: 3

image:

  repository: nginx

  tag: latest

service:

  type: ClusterIP
```

Instead of editing templates, change values here.

---

# Templates

Templates generate Kubernetes YAML files.

Example:

```yaml
replicas: {{ .Values.replicaCount }}
```

If:

```yaml
replicaCount: 5
```

Generated YAML becomes:

```yaml
replicas: 5
```

---

# Template Rendering

```
values.yaml

↓

Templates

↓

Rendered YAML

↓

kubectl apply
```

Helm renders templates locally before sending them to Kubernetes.

---

# Install a Chart

Example:

```bash
helm install prometheus prometheus-community/prometheus
```

Meaning:

```
Release Name

↓

prometheus

Chart

↓

prometheus-community/prometheus
```

---

# List Releases

```bash
helm list
```

---

# Search Repository

```bash
helm search repo prometheus
```

---

# Add Repository

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

---

# Update Repository

```bash
helm repo update
```

---

# Upgrade Release

```bash
helm upgrade prometheus prometheus-community/prometheus
```

---

# Rollback Release

View history:

```bash
helm history prometheus
```

Rollback:

```bash
helm rollback prometheus 1
```

Rollback restores a previous release revision.

---

# Uninstall

```bash
helm uninstall prometheus
```

Removes all resources managed by that release.

---

# Override Values

Instead of modifying `values.yaml`:

```bash
helm install nginx bitnami/nginx \
--set replicaCount=4
```

---

# Using a Custom Values File

```bash
helm install myapp ./mychart \
-f production-values.yaml
```

This keeps environment-specific settings separate.

---

# Create Your Own Chart

```bash
helm create mychart
```

Generated directory:

```
mychart/

templates/

values.yaml

Chart.yaml
```

---

# Validate Templates

Render without installing:

```bash
helm template mychart
```

Useful for debugging generated manifests.

---

# Lint a Chart

```bash
helm lint mychart
```

Checks for common chart issues.

---

# Package a Chart

```bash
helm package mychart
```

Creates:

```
mychart-1.0.0.tgz
```

This archive can be shared through a Helm repository.

---

# Install Local Chart

```bash
helm install myapp ./mychart
```

---

# Helm Upgrade Workflow

```
New Version

↓

helm upgrade

↓

Rolling Update

↓

Pods Updated
```

---

# Helm Release History

```
Revision 1

↓

Revision 2

↓

Revision 3

↓

Rollback to Revision 1
```

Helm stores release history, making rollbacks simple.

---

# Real Production Example

Install Prometheus:

```bash
helm repo add prometheus-community \
https://prometheus-community.github.io/helm-charts

helm repo update

helm install prometheus \
prometheus-community/prometheus
```

Install Grafana:

```bash
helm repo add grafana \
https://grafana.github.io/helm-charts

helm repo update

helm install grafana grafana/grafana
```

These are the same commands you used while learning.

---

# Useful Commands

## Repository

```bash
helm repo add
helm repo list
helm repo update
```

---

## Search

```bash
helm search repo nginx
```

---

## Install

```bash
helm install
```

---

## List

```bash
helm list
```

---

## Status

```bash
helm status <release>
```

---

## Upgrade

```bash
helm upgrade
```

---

## Rollback

```bash
helm rollback
```

---

## History

```bash
helm history
```

---

## Delete

```bash
helm uninstall
```

---

## Template

```bash
helm template
```

---

## Lint

```bash
helm lint
```

---

## Package

```bash
helm package
```

---

## Create

```bash
helm create
```

---

# Best Practices

✅ Keep environment-specific configuration in separate values files.

✅ Never hardcode secrets inside charts.

✅ Version your charts.

✅ Use `helm lint` before deployment.

✅ Use `helm template` to inspect generated manifests.

✅ Use rollback instead of manually restoring deleted resources.

---

# Common Mistakes

❌ Editing generated YAML instead of templates.

❌ Hardcoding image tags.

❌ Storing passwords in `values.yaml`.

❌ Forgetting to update repositories before installing charts.

❌ Using `latest` image tags in production.

---

# Helm vs Kubectl

| Helm | kubectl |
|-------|----------|
| Package manager | Kubernetes CLI |
| Uses Charts | Uses YAML manifests |
| Supports rollback | No built-in release history |
| Parameterized templates | Static manifests |
| Manages releases | Manages resources directly |

---

# Interview Questions

## Beginner

1. What is Helm?
2. Why do we use Helm?
3. What is a Helm Chart?
4. What is a Release?
5. What is `values.yaml`?

---

## Intermediate

6. Difference between Chart and Release?
7. What does `helm template` do?
8. How does Helm perform upgrades?
9. Why use `helm lint`?
10. How do you override chart values?

---

## Advanced

11. Explain the Helm architecture.
12. How are templates rendered?
13. How would you create a reusable Helm chart for multiple environments?
14. How does Helm rollback work?
15. Helm vs Kustomize—when would you choose each?

---

# 📝 Quick Revision

- Helm is the package manager for Kubernetes.
- A **Chart** is a reusable application package.
- A **Repository** stores charts.
- A **Release** is an installed chart.
- `values.yaml` holds configuration.
- Templates generate Kubernetes manifests.
- `helm install` deploys applications.
- `helm upgrade` updates releases.
- `helm rollback` restores previous revisions.
- `helm lint` validates charts.
- `helm template` renders manifests without installing.
- Helm simplifies application deployment and management in Kubernetes.
