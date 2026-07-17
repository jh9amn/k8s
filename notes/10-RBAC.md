# 📚 10 - Kubernetes Security (RBAC, Service Accounts & Security)

> **Security in Kubernetes is about ensuring that only the right users, applications, and components can access the right resources.**

---

# 📖 Table of Contents

1. Kubernetes Security Overview
2. Authentication vs Authorization
3. What is RBAC?
4. Role
5. ClusterRole
6. RoleBinding
7. ClusterRoleBinding
8. Service Accounts
9. Admission Controllers
10. Pod Security Standards
11. Network Policies
12. Secret Security
13. Security Context
14. Useful Commands
15. Best Practices
16. Common Mistakes
17. Interview Questions
18. Quick Revision

---

# Kubernetes Security Layers

Security is applied in multiple layers.

```
                 User / Application
                        │
                        ▼
              Authentication
                        │
                        ▼
               Authorization (RBAC)
                        │
                        ▼
           Admission Controllers
                        │
                        ▼
        API Server accepts/rejects request
                        │
                        ▼
             Pod & Container Security
                        │
                        ▼
               Network Policies
                        │
                        ▼
             Worker Node Security
```

---

# Authentication

Authentication answers one question:

```
Who are you?
```

Examples:

- User certificate
- Bearer Token
- OpenID Connect (OIDC)
- Cloud IAM integration (AWS, Azure, GCP)

If authentication fails:

```
Request

↓

Rejected
```

---

# Authorization

Authorization answers:

```
What are you allowed to do?
```

Example:

```
Developer

↓

Can View Pods

↓

Cannot Delete Cluster
```

Authorization happens **after** authentication.

---

# Authentication vs Authorization

| Authentication | Authorization |
|---------------|---------------|
| Verifies identity | Verifies permissions |
| "Who are you?" | "What can you do?" |
| First step | Second step |

---

# What is RBAC?

RBAC = **Role-Based Access Control**

It controls who can perform which actions on Kubernetes resources.

Example:

```
Developer

↓

Read Pods

Create Deployments

❌ Delete Nodes
```

---

# RBAC Components

```
User / ServiceAccount

↓

Role or ClusterRole

↓

RoleBinding or ClusterRoleBinding
```

Permissions are granted by binding identities to roles.

---

# Role

A **Role** grants permissions **within a single namespace**.

Example:

```yaml
kind: Role

rules:

- apiGroups: [""]

  resources: ["pods"]

  verbs:

  - get

  - list

  - watch
```

This allows reading Pods in one namespace.

---

# ClusterRole

A **ClusterRole** grants permissions that can apply cluster-wide or to non-namespaced resources.

Example:

```yaml
kind: ClusterRole
```

Common uses:

- Read Nodes
- Read Namespaces
- Cluster administrators

---

# Role vs ClusterRole

| Role | ClusterRole |
|------|-------------|
| Namespace-scoped | Cluster-scoped |
| Limited to one namespace | Can span all namespaces |
| Cannot manage Nodes | Can include cluster-wide resources |

---

# RoleBinding

Connects:

```
User

↓

Role
```

Only works inside one namespace.

Example:

```yaml
kind: RoleBinding
```

---

# ClusterRoleBinding

Connects:

```
User

↓

ClusterRole
```

Permissions apply cluster-wide (or wherever the ClusterRole grants access).

Example:

```yaml
kind: ClusterRoleBinding
```

---

# RBAC Architecture

```
Developer

↓

RoleBinding

↓

Role

↓

Pods
```

Administrator:

```
Admin

↓

ClusterRoleBinding

↓

ClusterRole

↓

Entire Cluster
```

---

# Service Accounts

Applications running inside Pods need identities too.

Kubernetes provides **Service Accounts**.

```
Pod

↓

Service Account

↓

API Server
```

Unlike user accounts, Service Accounts are intended for workloads running in the cluster.

---

# Default Service Account

Every namespace automatically has:

```
default
```

If you don't specify one, Pods use the default Service Account.

---

# Create a Service Account

```bash
kubectl create serviceaccount app-sa
```

---

# View Service Accounts

```bash
kubectl get sa
```

---

# Use a Service Account

```yaml
spec:

  serviceAccountName: app-sa
```

Now the Pod authenticates to the Kubernetes API as `app-sa`.

---

# Admission Controllers

After authentication and authorization, Admission Controllers can validate or modify requests before objects are stored.

```
Request

↓

Authentication

↓

Authorization

↓

Admission Controllers

↓

Object Stored
```

Examples:

- Mutating admission (modify requests)
- Validating admission (approve/reject requests)

Examples of built-in controllers include `LimitRanger`, `ResourceQuota`, and `DefaultStorageClass`.

---

# Pod Security Standards (PSS)

Pod Security Standards define recommended security levels for Pods.

Levels:

## Privileged

Minimal restrictions.

Suitable only for trusted infrastructure workloads.

---

## Baseline

Blocks many risky configurations while remaining broadly compatible.

---

## Restricted

Most secure level.

Encourages:

- Non-root containers
- Restricted capabilities
- Read-only filesystems where appropriate

---

# Network Policies

By default, many Kubernetes network plugins allow Pods to communicate freely.

Network Policies restrict traffic.

```
Pod A

↓

Allowed

↓

Pod B
```

or

```
Pod A

↓

Denied

↓

Pod C
```

You can control:

- Ingress traffic
- Egress traffic

> **Important:** Network Policies require a compatible Container Network Interface (CNI) plugin (such as Calico or Cilium) that implements them.

---

# Secret Security

Secrets store sensitive information.

Examples:

- Passwords
- API Keys
- TLS Certificates

Best practices:

- Limit access with RBAC
- Enable encryption at rest
- Rotate credentials regularly
- Avoid storing secrets in Git repositories

---

# Security Context

A Security Context defines security settings for a Pod or container.

Example:

```yaml
securityContext:

  runAsNonRoot: true

  runAsUser: 1000

  readOnlyRootFilesystem: true

  allowPrivilegeEscalation: false
```

This helps reduce the impact of compromised containers.

---

# Useful Commands

## View Roles

```bash
kubectl get roles
```

---

## View ClusterRoles

```bash
kubectl get clusterroles
```

---

## View RoleBindings

```bash
kubectl get rolebindings
```

---

## View ClusterRoleBindings

```bash
kubectl get clusterrolebindings
```

---

## View Service Accounts

```bash
kubectl get sa
```

---

## Describe Role

```bash
kubectl describe role <role-name>
```

---

## Describe ClusterRole

```bash
kubectl describe clusterrole <clusterrole-name>
```

---

## Check Permissions

```bash
kubectl auth can-i get pods
```

Example:

```bash
kubectl auth can-i delete nodes
```

---

## Check Permissions as Another Identity

```bash
kubectl auth can-i list pods \
  --as=system:serviceaccount:default:app-sa
```

---

# Production Example

```
Monitoring Pod

↓

Service Account

↓

Read Metrics

↓

Cannot Delete Pods
```

---

```
Developer

↓

Role

↓

Deploy Applications

↓

Cannot Delete Nodes
```

---

```
Cluster Admin

↓

ClusterRole

↓

Full Cluster Access
```

---

# Best Practices

✅ Grant the minimum permissions required (Principle of Least Privilege).

✅ Use dedicated Service Accounts for applications.

✅ Use RBAC instead of giving broad admin access.

✅ Enable encryption at rest for Secrets.

✅ Use Network Policies to restrict Pod communication.

✅ Keep Kubernetes and container images updated.

---

# Common Mistakes

❌ Running every application with cluster-admin privileges.

❌ Using the default Service Account for all workloads.

❌ Giving write access when read-only access is enough.

❌ Storing production secrets in source control.

❌ Running containers as the root user without a valid reason.

---

# Interview Questions

## Beginner

1. What is RBAC?
2. Difference between authentication and authorization?
3. What is a Role?
4. What is a ClusterRole?
5. What is a Service Account?

---

## Intermediate

6. Difference between RoleBinding and ClusterRoleBinding?
7. Why do Pods use Service Accounts?
8. What are Admission Controllers?
9. Explain Pod Security Standards.
10. What are Network Policies?

---

## Advanced

11. Explain the complete security flow from request to API Server.
12. How does RBAC work internally?
13. How would you secure Secrets in production?
14. How would you design RBAC for developers, DevOps engineers, and applications?
15. Why is the Principle of Least Privilege important?

---

# 📝 Quick Revision

- Authentication verifies identity.
- Authorization verifies permissions.
- RBAC controls access to Kubernetes resources.
- Roles are namespace-scoped; ClusterRoles can apply cluster-wide.
- RoleBindings bind Roles; ClusterRoleBindings bind ClusterRoles.
- Service Accounts provide identities for Pods.
- Admission Controllers validate or modify requests.
- Pod Security Standards improve workload security.
- Network Policies restrict Pod-to-Pod communication.
- Security Context hardens container execution.
- Follow the Principle of Least Privilege.
