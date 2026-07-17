# 📚 05 - ConfigMaps & Secrets

> **Never hardcode configuration inside your application image.**
>
> Kubernetes provides **ConfigMaps** and **Secrets** to separate configuration from application code.

---

# 📖 Table of Contents

- Why Configuration Matters
- What is a ConfigMap?
- ConfigMap Architecture
- Creating ConfigMaps
- Using ConfigMaps
- Environment Variables vs Volume Mounts
- Updating ConfigMaps
- What are Secrets?
- Secret Types
- Creating Secrets
- Using Secrets
- ConfigMaps vs Secrets
- Commands Cheat Sheet
- Best Practices
- Common Errors
- Interview Questions
- Quick Revision

---

# Why Do We Need ConfigMaps?

Imagine you have an application.

```text
Database URL = localhost
Port = 8080
Environment = Development
```

You build the Docker image.

Now you deploy it to Production.

Production values should be:

```text
Database URL = mysql.company.com
Port = 80
Environment = Production
```

Without ConfigMaps:

```
Modify Source Code

↓

Rebuild Docker Image

↓

Push Image

↓

Redeploy
```

Every configuration change requires a new image.

This is inefficient.

---

# Solution

Move configuration outside the image.

```
Application

↓

Reads ConfigMap

↓

Uses Configuration
```

Now you only update Kubernetes configuration.

---

# What is a ConfigMap?

A **ConfigMap** stores **non-sensitive configuration data** as key-value pairs.

Examples:

- Application Name
- Environment
- Port Number
- API URL
- Feature Flags
- Log Level

Think of it as a configuration file managed by Kubernetes.

---

# ConfigMap Architecture

```
           ConfigMap

        APP_NAME=Store

        PORT=8080

        LOG_LEVEL=INFO

              │

              ▼

          Deployment

              │

              ▼

             Pods
```

One ConfigMap can be used by multiple Pods.

---

# Creating a ConfigMap

### YAML

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: app-config

data:
  APP_NAME: "My Store"
  PORT: "8080"
  LOG_LEVEL: "INFO"
```

Apply it:

```bash
kubectl apply -f configmap.yaml
```

---

### Create from Command Line

```bash
kubectl create configmap app-config \
  --from-literal=APP_NAME="My Store" \
  --from-literal=PORT=8080
```

---

### Create from File

```bash
kubectl create configmap app-config \
  --from-file=application.properties
```

---

# Viewing ConfigMaps

List:

```bash
kubectl get cm
```

Describe:

```bash
kubectl describe cm app-config
```

YAML:

```bash
kubectl get cm app-config -o yaml
```

---

# Using ConfigMap as Environment Variables

Deployment example:

```yaml
env:

- name: APP_NAME
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: APP_NAME

- name: PORT
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: PORT
```

Application reads them as normal environment variables.

---

# Import All Keys

Instead of one by one:

```yaml
envFrom:

- configMapRef:
    name: app-config
```

Every key becomes an environment variable.

---

# Mount ConfigMap as Files

Sometimes applications expect configuration files.

```
ConfigMap

↓

Mounted Volume

↓

/etc/config
```

Example:

```yaml
volumes:

- name: config-volume

  configMap:

    name: app-config

containers:

- volumeMounts:

  - name: config-volume

    mountPath: /etc/config
```

---

# Environment Variables vs Mounted Files

| Environment Variables | Mounted Files |
|-----------------------|---------------|
| Easy to use | Good for large configs |
| Restart usually required to pick up changes | Mounted files can reflect updates automatically (application behavior may vary) |
| Best for small settings | Best for configuration files |

---

# Updating a ConfigMap

```
kubectl edit cm app-config
```

or

```bash
kubectl apply -f configmap.yaml
```

> **Important:** If a ConfigMap is consumed as environment variables, existing Pods won't automatically get the new values. Typically, you restart the Deployment:
>
> ```bash
> kubectl rollout restart deployment <deployment-name>
> ```
>
> Mounted ConfigMap volumes are updated automatically after a short delay, but your application must be able to reload the changed files.

---

# What is a Secret?

A **Secret** stores **sensitive data**.

Examples:

- Passwords
- API Keys
- Database Credentials
- Certificates
- Tokens

Never store these in a ConfigMap.

---

# Secret Architecture

```
           Secret

     DB_USER=admin

     DB_PASSWORD=*****

            │

            ▼

        Deployment

            │

            ▼

           Pods
```

---

# Secret Types

| Type | Purpose |
|------|----------|
| Opaque | Generic key-value Secret |
| kubernetes.io/tls | TLS certificates |
| kubernetes.io/dockerconfigjson | Container registry credentials |
| kubernetes.io/basic-auth | Username & password |
| kubernetes.io/ssh-auth | SSH credentials |

---

# Creating a Secret

Command:

```bash
kubectl create secret generic db-secret \
  --from-literal=username=admin \
  --from-literal=password=123456
```

---

YAML:

```yaml
apiVersion: v1
kind: Secret

metadata:
  name: db-secret

type: Opaque

data:
  username: YWRtaW4=
  password: MTIzNDU2
```

These values are Base64 encoded.

> **Note:** Base64 is **encoding**, not encryption. Kubernetes Secrets provide an API object for sensitive data, but for stronger protection you should also enable **encryption at rest** in the cluster and follow good access-control practices.

---

# Using Secrets

Environment Variable:

```yaml
env:

- name: DB_PASSWORD

  valueFrom:

    secretKeyRef:

      name: db-secret

      key: password
```

---

Mounted File:

```yaml
volumes:

- name: secret-volume

  secret:

    secretName: db-secret
```

---

# ConfigMap vs Secret

| ConfigMap | Secret |
|-----------|--------|
| Non-sensitive data | Sensitive data |
| Plain text values | Base64-encoded in manifests |
| App config | Passwords, keys, tokens |
| Public configuration | Restricted access via RBAC |

---

# Commands Cheat Sheet

### ConfigMap

```bash
kubectl get cm
```

```bash
kubectl describe cm
```

```bash
kubectl edit cm
```

```bash
kubectl delete cm
```

---

### Secret

```bash
kubectl get secrets
```

```bash
kubectl describe secret
```

```bash
kubectl delete secret
```

View YAML:

```bash
kubectl get secret db-secret -o yaml
```

---

# Best Practices

✅ Keep application code and configuration separate.

✅ Use ConfigMaps only for non-sensitive values.

✅ Store passwords and API keys in Secrets.

✅ Use RBAC to limit Secret access.

✅ Rotate Secrets periodically.

✅ Avoid committing Secret manifests with real credentials to Git.

---

# Common Errors

## Pod Doesn't See ConfigMap

Check:

```bash
kubectl describe pod
```

Verify the ConfigMap name and key exist.

---

## Secret Key Missing

Check:

```bash
kubectl describe secret
```

---

## ConfigMap Updated but App Still Uses Old Value

Reason:

Environment variables don't update in running Pods.

Fix:

```bash
kubectl rollout restart deployment <deployment-name>
```

---

# Interview Questions

### Beginner

1. What is a ConfigMap?
2. What is a Secret?
3. Difference between ConfigMap and Secret?
4. Can multiple Pods use one ConfigMap?
5. Can multiple Pods use one Secret?

### Intermediate

6. How do you inject a ConfigMap into a Pod?
7. How do you mount a Secret as a volume?
8. Why shouldn't passwords be stored in ConfigMaps?
9. What happens when a ConfigMap changes?

### Advanced

10. Explain Secret types.
11. Is Base64 encryption?
12. How would you rotate Secrets in production?
13. How can RBAC protect Secrets?

---

# 📝 Quick Revision

- ConfigMaps store non-sensitive configuration.
- Secrets store sensitive information.
- Both can be exposed as environment variables or mounted files.
- ConfigMaps help avoid rebuilding images for configuration changes.
- Restart Deployments when environment-variable-based ConfigMaps change.
- Base64 is encoding, not encryption.
- Use RBAC and encryption at rest to improve Secret security.
