
# 📚 08 - Kubernetes Ingress & Ingress Controller

> **Ingress provides a smart entry point into your Kubernetes cluster.**
>
> Instead of exposing every application using a separate NodePort or LoadBalancer Service, Ingress lets multiple applications share a single external entry point with routing based on hostnames and URL paths.

---

# 📖 Table of Contents

1. Why Do We Need Ingress?
2. Problems with NodePort & LoadBalancer
3. What is Ingress?
4. What is an Ingress Controller?
5. Request Flow
6. Ingress Architecture
7. Host-Based Routing
8. Path-Based Routing
9. TLS / HTTPS
10. Default Backend
11. NGINX Ingress Controller
12. Minikube Ingress
13. Useful Commands
14. Troubleshooting
15. Best Practices
16. Interview Questions
17. Quick Revision

---

# Why Do We Need Ingress?

Suppose you have three applications.

```
Frontend

Backend API

Admin Panel
```

Without Ingress:

```
Frontend

↓

LoadBalancer 1

-------------------

Backend

↓

LoadBalancer 2

-------------------

Admin

↓

LoadBalancer 3
```

Problems:

- Expensive (cloud providers often charge per LoadBalancer)
- Difficult to manage
- Many public IP addresses
- More firewall rules
- Harder SSL management

---

# Solution

Use one Ingress.

```
Internet

↓

Ingress

↓

Frontend

Backend

Admin
```

One IP.

One LoadBalancer (or one NodePort in local setups).

Multiple applications.

---

# What is Ingress?

Ingress is a Kubernetes API object that defines **HTTP/HTTPS routing rules** for traffic entering the cluster.

Ingress **does not forward traffic by itself**.

It only stores routing rules.

Example:

```
example.com

↓

Frontend Service
```

```
api.example.com

↓

Backend Service
```

---

# Important Point

Ingress is only configuration.

It needs an **Ingress Controller**.

Without a controller:

```
Ingress

↓

No Controller

↓

Nothing Happens ❌
```

---

# What is an Ingress Controller?

The Ingress Controller is a Pod (or set of Pods) that watches Ingress resources and configures a reverse proxy (such as NGINX) to route traffic.

Popular controllers:

- NGINX Ingress Controller
- HAProxy Ingress
- Traefik
- Kong
- Istio Gateway (uses Gateway API concepts rather than the traditional Ingress API)

---

# Request Flow

```
Browser

↓

DNS

↓

Ingress Controller

↓

Ingress Rules

↓

Service

↓

Pods
```

---

# Complete Architecture

```
             Internet

                 │

                 ▼

        Ingress Controller

                 │

        Reads Ingress Rules

                 │

      ┌──────────┴──────────┐

      ▼                     ▼

 Frontend Service     Backend Service

      ▼                     ▼

    Pods                  Pods
```

---

# Host-Based Routing

Different domains route to different applications.

```
shop.example.com

↓

Frontend
```

```
api.example.com

↓

Backend API
```

Example:

```yaml
rules:

- host: shop.example.com

- host: api.example.com
```

---

# Path-Based Routing

Same domain.

Different URL paths.

```
example.com/

↓

Frontend
```

```
example.com/api

↓

Backend
```

Example:

```yaml
rules:

- host: example.com

  http:

    paths:

    - path: /

    - path: /api
```

---

# Example Ingress YAML

```yaml
apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:
  name: app-ingress

spec:

  ingressClassName: nginx

  rules:

  - host: foo.bar.com

    http:

      paths:

      - path: /

        pathType: Prefix

        backend:

          service:

            name: frontend-service

            port:

              number: 80
```

Apply:

```bash
kubectl apply -f ingress.yaml
```

---

# Ingress Classes

An `IngressClass` tells Kubernetes which controller should manage an Ingress.

Example:

```yaml
ingressClassName: nginx
```

Without a matching controller, the Ingress won't function.

---

# Default Backend

If no rule matches:

```
Request

↓

No Matching Rule

↓

Default Backend

↓

404
```

---

# TLS / HTTPS

Ingress can terminate TLS.

```
Browser

↓

HTTPS

↓

Ingress Controller

↓

HTTP

↓

Service
```

TLS example:

```yaml
tls:

- hosts:

  - example.com

  secretName: tls-secret
```

The TLS certificate is stored in a Kubernetes Secret.

---

# NGINX Ingress Controller

Most popular controller.

Responsibilities:

- Watches Ingress resources
- Updates NGINX configuration
- Performs routing
- SSL termination
- Load balancing
- URL rewrites (via annotations)

---

# Minikube Setup

Enable Ingress:

```bash
minikube addons enable ingress
```

Check Pods:

```bash
kubectl get pods -n ingress-nginx
```

Check Service:

```bash
kubectl get svc -n ingress-nginx
```

View Ingress:

```bash
kubectl get ingress
```

Describe:

```bash
kubectl describe ingress
```

---

# Testing in Minikube

Get cluster IP:

```bash
minikube ip
```

Example:

```
192.168.49.2
```

Update your hosts file:

```
192.168.49.2 foo.bar.com
```

Now access:

```
http://foo.bar.com
```

---

# Ingress vs Service

| Service | Ingress |
|----------|---------|
| Connects to Pods | Routes external HTTP/HTTPS traffic |
| Layer 4 (TCP/UDP) | Layer 7 (HTTP/HTTPS) |
| One application | Multiple applications |
| Doesn't inspect URLs | Can route by host/path |

---

# NodePort vs Ingress

| NodePort | Ingress |
|----------|---------|
| One port per Service | One entry point for many Services |
| No URL routing | Host & path routing |
| Manual port management | Cleaner and easier to manage |
| Mainly development | Common in production |

---

# LoadBalancer vs Ingress

| LoadBalancer | Ingress |
|--------------|---------|
| Exposes one Service | Can expose many Services |
| Layer 4 | Layer 7 |
| Usually one IP per Service | Typically one IP for multiple apps |

---

# Useful Commands

Create:

```bash
kubectl apply -f ingress.yaml
```

View:

```bash
kubectl get ingress
```

Describe:

```bash
kubectl describe ingress
```

Delete:

```bash
kubectl delete ingress app-ingress
```

View controller Pods:

```bash
kubectl get pods -n ingress-nginx
```

View controller logs:

```bash
kubectl logs -n ingress-nginx <controller-pod-name>
```

Watch:

```bash
kubectl get ingress -w
```

---

# Troubleshooting

## 404 Not Found

Possible causes:

- Wrong host
- Wrong path
- No matching rule
- Default backend responding

Check:

```bash
kubectl describe ingress
```

---

## Service Not Found

Check:

```bash
kubectl get svc
```

Verify the Service name and port referenced by the Ingress.

---

## Controller Missing

Check:

```bash
kubectl get pods -n ingress-nginx
```

---

## Host Doesn't Resolve

Check:

```bash
minikube ip
```

Update your hosts file accordingly.

---

## Backend Has No Endpoints

Check:

```bash
kubectl get endpoints
```

If empty, verify that the Service selector matches the Pod labels.

---

# Best Practices

✅ Use one Ingress for multiple applications when appropriate.

✅ Use TLS (HTTPS) in production.

✅ Keep routing rules simple and organized.

✅ Use DNS instead of editing the hosts file in production.

✅ Use an appropriate Ingress Controller for your environment.

---

# Common Mistakes

❌ Creating an Ingress without installing an Ingress Controller.

❌ Pointing an Ingress to the wrong Service port.

❌ Forgetting `ingressClassName` when multiple controllers exist.

❌ Assuming Ingress supports arbitrary TCP/UDP traffic. (Traditional Ingress is designed for HTTP/HTTPS.)

---

# Interview Questions

## Beginner

1. What is Ingress?
2. Why do we need Ingress?
3. What is an Ingress Controller?
4. Difference between Service and Ingress?
5. Difference between NodePort and Ingress?

---

## Intermediate

6. Explain host-based routing.
7. Explain path-based routing.
8. What is an IngressClass?
9. How does TLS work with Ingress?
10. Why doesn't an Ingress work without a controller?

---

## Advanced

11. Explain the complete request flow from browser to Pod.
12. How does the NGINX Ingress Controller work internally?
13. What happens if two Ingress resources define conflicting rules?
14. When would you use the Kubernetes Gateway API instead of traditional Ingress?

---

# 📝 Quick Revision

- Ingress manages HTTP/HTTPS routing into the cluster.
- Ingress needs an Ingress Controller to function.
- The controller watches Ingress resources and configures a reverse proxy.
- Ingress supports host-based and path-based routing.
- One Ingress can expose multiple Services.
- TLS certificates are stored in Kubernetes Secrets.
- NGINX is the most commonly used Ingress Controller.
- In Minikube, enable the controller using `minikube addons enable ingress`.
