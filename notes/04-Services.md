# 📚 04 - Kubernetes Services

> **Pods are temporary. Services provide a stable way to access Pods.**

---

# 📖 Table of Contents

- Why Do We Need Services?
- What is a Service?
- How Services Work
- Service Architecture
- Types of Services
- ClusterIP
- NodePort
- LoadBalancer
- ExternalName
- Headless Service
- Endpoints
- kube-proxy & Service Routing
- Service YAML
- kubectl Commands
- Debugging
- Best Practices
- Interview Questions
- Quick Revision

---

# The Problem Without Services

Suppose you deploy an application.

```
Deployment

↓

Pod

↓

IP = 10.244.0.15
```

Your frontend connects to:

```
10.244.0.15
```

Now the Pod crashes.

```
Old Pod

↓

Deleted

↓

New Pod

↓

IP = 10.244.0.27
```

Your frontend still tries to connect to:

```
10.244.0.15 ❌
```

Application breaks.

---

# Solution: Service

Instead of talking directly to Pods:

```
Client

↓

Service

↓

Pods
```

The Service has a stable virtual IP (ClusterIP).

```
Client

↓

10.96.0.15 (Service)

↓

Pod A

Pod B

Pod C
```

Even if Pods change, the Service IP stays the same.

---

# What is a Kubernetes Service?

A **Service** is a stable network endpoint that exposes one or more Pods.

It:

- Provides a fixed IP address.
- Provides a DNS name.
- Load balances traffic.
- Selects Pods using labels.

---

# How Services Work

```
Pods

app=nginx

↓

Service

selector:

app=nginx

↓

Traffic forwarded
```

A Service doesn't know Pod names.

It only knows **labels**.

---

# Internal Architecture

```
               Client

                  │

                  ▼

          Service (ClusterIP)

                  │

        kube-proxy on every Node

                  │

        ┌─────────┴─────────┐

        ▼                   ▼

      Pod A              Pod B
```

---

# Types of Services

| Type | Used For |
|-------|----------|
| ClusterIP | Internal communication |
| NodePort | External access (development/testing) |
| LoadBalancer | External access (cloud environments) |
| ExternalName | Maps to an external DNS name |
| Headless | Direct Pod access (no ClusterIP) |

---

# 1. ClusterIP

Default Service type.

Accessible only inside the cluster.

```
Frontend Pod

↓

ClusterIP

↓

Backend Pods
```

Example YAML

```yaml
apiVersion: v1
kind: Service

metadata:
  name: backend-service

spec:
  selector:
    app: backend

  ports:
    - port: 80
      targetPort: 8080
```

Create:

```bash
kubectl apply -f service.yaml
```

---

# 2. NodePort

Exposes a Service on every Node.

```
Browser

↓

Node IP:30080

↓

NodePort Service

↓

Pods
```

Example:

```yaml
spec:
  type: NodePort

  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30080
```

Access:

```
http://<Node-IP>:30080
```

Example from your setup:

```
http://192.168.49.2:31955
```

---

# 3. LoadBalancer

Used in cloud providers.

```
Internet

↓

Cloud Load Balancer

↓

Cluster

↓

Pods
```

Example:

```yaml
spec:
  type: LoadBalancer
```

Works on:

- AWS
- Azure
- Google Cloud

**Minikube note:** `type: LoadBalancer` doesn't automatically create a cloud load balancer. You can simulate it with:

```bash
minikube tunnel
```

---

# NodePort vs LoadBalancer

| NodePort | LoadBalancer |
|----------|--------------|
| Built into Kubernetes | Uses a cloud provider |
| Uses Node IP + Port | Uses an external IP |
| Development/testing | Production |
| Manual access | Automatic external access |

---

# 4. ExternalName

Maps a Kubernetes Service to an external DNS name.

Example:

```
database.example.com
```

YAML:

```yaml
spec:
  type: ExternalName

  externalName: database.example.com
```

No Pods are selected.

---

# 5. Headless Service

A Headless Service **does not assign a ClusterIP**.

```yaml
clusterIP: None
```

Useful for:

- StatefulSets
- Databases
- Direct Pod-to-Pod communication

Example:

```
mysql-0

mysql-1

mysql-2
```

Each Pod gets its own DNS record.

---

# Endpoints

A Service forwards traffic to **Endpoints**.

Example:

```
Service

↓

Endpoints

↓

Pod A

Pod B

Pod C
```

View them:

```bash
kubectl get endpoints
```

---

# kube-proxy and Services

kube-proxy runs on every worker node.

Responsibilities:

- Watches Service changes.
- Updates networking rules.
- Routes traffic to Pods.
- Performs load balancing.

```
Client

↓

Service IP

↓

kube-proxy

↓

iptables/IPVS

↓

Pod
```

---

# Service Commands

## List Services

```bash
kubectl get svc
```

---

## Describe Service

```bash
kubectl describe svc backend-service
```

---

## Get Endpoints

```bash
kubectl get endpoints
```

---

## Delete Service

```bash
kubectl delete svc backend-service
```

---

## Expose a Deployment

```bash
kubectl expose deployment nginx \
--type=NodePort \
--port=80
```

---

## Port Forward

```bash
kubectl port-forward svc/backend-service 8080:80
```

Access:

```
http://localhost:8080
```

---

## Watch Services

```bash
kubectl get svc -w
```

---

# Debugging Services

Step 1

```bash
kubectl get svc
```

↓

Step 2

```bash
kubectl describe svc
```

↓

Step 3

```bash
kubectl get endpoints
```

↓

Step 4

```bash
kubectl get pods --show-labels
```

↓

Step 5

```bash
kubectl logs
```

---

# Common Errors

## No Endpoints

Reason:

Labels don't match.

Check:

```bash
kubectl get pods --show-labels
```

---

## Connection Refused

Check:

- Pod is running.
- Correct targetPort.
- Application listens on the expected port.

---

## NodePort Not Working

Check:

```bash
minikube service <service-name> --url
```

Or verify:

```bash
minikube ip
kubectl get svc
```

---

# Best Practices

✅ Use **ClusterIP** for internal communication.

✅ Use **NodePort** only for learning or testing.

✅ Use **LoadBalancer** in production cloud environments.

✅ Prefer **Ingress** instead of exposing many NodePort Services.

✅ Use labels consistently.

---

# 🔥 Pro Tips

- Use `kubectl get endpoints` when a Service exists but traffic isn't reaching Pods.
- A Service **doesn't create Pods**—it only routes traffic.
- `kubectl port-forward` is a quick way to access a ClusterIP Service without exposing it externally.

---

# ⚠️ Common Mistakes

❌ Accessing Pods directly by IP.

✔️ Always use a Service.

---

❌ Forgetting labels.

Example:

Service:

```yaml
selector:
  app: backend
```

Pod:

```yaml
labels:
  app: api
```

Result:

No Endpoints ❌

---

❌ Assuming NodePort is secure enough for production.

✔️ Prefer Ingress or a cloud LoadBalancer with proper security controls.

---

# Interview Questions

### Beginner

1. What is a Service?
2. Why do we need Services?
3. What problem do Services solve?
4. What is ClusterIP?
5. What is NodePort?

### Intermediate

6. Explain the different Service types.
7. What are Endpoints?
8. How does kube-proxy route traffic?
9. Why are labels important for Services?
10. Difference between NodePort and LoadBalancer?

### Advanced

11. Explain Headless Services.
12. Explain ExternalName Services.
13. How does kube-proxy use iptables or IPVS?
14. What happens internally when a Service receives traffic?

---

# 📝 Quick Revision

- Services provide a stable network endpoint for Pods.
- Services select Pods using labels.
- ClusterIP is the default internal Service.
- NodePort exposes a Service on every node.
- LoadBalancer integrates with cloud providers.
- Headless Services provide direct Pod DNS.
- Endpoints represent the Pods behind a Service.
- kube-proxy programs the node's networking rules to forward Service traffic.
