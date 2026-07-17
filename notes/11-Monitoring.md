# 📚 11 - Kubernetes Monitoring, Logging & Troubleshooting

> **If you can't observe your cluster, you can't operate it.**
>
> Monitoring tells you **what is happening**, logging tells you **why it happened**, and troubleshooting helps you **fix the problem**.

---

# 📖 Table of Contents

1. What is Observability?
2. Monitoring vs Logging vs Tracing
3. Kubernetes Monitoring Architecture
4. Metrics Server
5. Prometheus
6. kube-state-metrics
7. Node Exporter
8. Grafana
9. Logging
10. Events
11. Probes (Liveness, Readiness & Startup)
12. Common Pod States
13. Troubleshooting Workflow
14. Useful Commands
15. Best Practices
16. Common Errors
17. Interview Questions
18. Quick Revision

---

# What is Observability?

Observability helps answer three questions:

```
Is my application healthy?

Why is it failing?

Where is the problem?
```

Observability consists of:

```
Metrics

↓

Logs

↓

Traces
```

---

# Monitoring vs Logging vs Tracing

| Monitoring | Logging | Tracing |
|------------|---------|----------|
| Numeric metrics | Text records | Tracks request flow |
| CPU, Memory | Errors, warnings | Microservices debugging |
| Shows trends | Explains events | Finds latency bottlenecks |

Example:

```
CPU = 95%

↓

Monitoring detects problem

↓

Logs explain why

↓

Trace shows which service caused it
```

---

# Kubernetes Monitoring Architecture

```
                    Kubernetes Cluster

        ┌─────────────────────────────────────┐

             kubelet      kube-state-metrics

                 │                │

                 ▼                ▼

             Prometheus <─────────┘

                 │

                 ▼

              Grafana

                 │

                 ▼

             Dashboards
```

---

# Metrics Server

The **Metrics Server** collects basic CPU and Memory metrics from kubelets.

Used by:

- `kubectl top`
- Horizontal Pod Autoscaler (HPA)

Example:

```bash
kubectl top nodes
```

```bash
kubectl top pods
```

> Metrics Server provides **current resource usage**, not long-term historical metrics.

---

# Prometheus

Prometheus is the most widely used monitoring system for Kubernetes.

Responsibilities:

- Scrapes metrics
- Stores time-series data
- Executes PromQL queries
- Generates alerts (typically through Alertmanager)

Architecture:

```
Application

↓

/metrics endpoint

↓

Prometheus

↓

Time-Series Database
```

---

# How Prometheus Works

```
Every 15-30 seconds

↓

HTTP GET /metrics

↓

Application Returns Metrics

↓

Prometheus Stores Data
```

This process is called **scraping**.

---

# kube-state-metrics

kube-state-metrics exposes metrics about Kubernetes objects.

Examples:

- Pod status
- Deployment replicas
- StatefulSets
- Nodes
- Jobs
- PersistentVolumes

It **does not** report CPU or memory usage.

Instead, it reports the **state** of Kubernetes resources.

---

# Node Exporter

Node Exporter exposes operating system metrics.

Examples:

- CPU usage
- Disk usage
- Memory
- Network
- File systems

Flow:

```
Linux Node

↓

Node Exporter

↓

Prometheus
```

---

# Grafana

Grafana visualizes metrics.

```
Prometheus

↓

Grafana

↓

Dashboard
```

Example dashboard:

```
CPU Usage

Memory Usage

Pod Count

Network Traffic

Disk Usage
```

Grafana queries Prometheus using PromQL.

---

# Logging in Kubernetes

Logs answer:

```
Why did my application fail?
```

View logs:

```bash
kubectl logs <pod-name>
```

For multi-container Pods:

```bash
kubectl logs <pod-name> -c <container-name>
```

Follow logs:

```bash
kubectl logs -f <pod-name>
```

Previous container logs (after a restart):

```bash
kubectl logs --previous <pod-name>
```

---

# Events

Events show what Kubernetes is doing.

View events:

```bash
kubectl get events
```

Sorted:

```bash
kubectl get events \
--sort-by=.metadata.creationTimestamp
```

Examples:

```
Pulling Image

Started Container

Failed Scheduling

BackOff
```

---

# Describe Command

The most useful troubleshooting command.

```bash
kubectl describe pod <pod-name>
```

Shows:

- Scheduling events
- Image pull errors
- Volume mount errors
- Probes
- Resource requests/limits
- Conditions

---

# Probes

Probes let Kubernetes determine whether an application is healthy and ready.

## Liveness Probe

Question:

```
Is the application alive?
```

If it fails:

```
Restart Container
```

---

## Readiness Probe

Question:

```
Can the application receive traffic?
```

If it fails:

```
Remove Pod from Service endpoints
```

The container keeps running, but traffic is not sent to it.

---

## Startup Probe

Question:

```
Has the application finished starting?
```

Useful for slow-starting applications.

Until it succeeds, liveness and readiness checks are delayed.

---

# Probe Comparison

| Probe | Purpose | Failure Result |
|--------|---------|----------------|
| Liveness | Detect hung applications | Restart container |
| Readiness | Control traffic | Remove from Service endpoints |
| Startup | Handle slow startup | Continue startup checking |

---

# Common Pod States

## Running

Application is healthy.

---

## Pending

Pod has not been scheduled or is waiting for resources.

Possible causes:

- No available nodes
- PVC Pending
- Unsatisfied affinity rules

---

## CrashLoopBackOff

Container starts.

↓

Crashes.

↓

Restarts repeatedly.

Common causes:

- Application bug
- Wrong command
- Missing configuration

---

## ImagePullBackOff

Kubernetes cannot pull the container image.

Common causes:

- Wrong image name
- Wrong tag
- Private registry authentication missing
- Registry unavailable

---

## ErrImagePull

Initial image download failed.

Often followed by `ImagePullBackOff`.

---

# Troubleshooting Workflow

```
Application Not Working

↓

kubectl get pods

↓

kubectl describe pod

↓

kubectl logs

↓

kubectl get events

↓

kubectl describe svc

↓

kubectl get endpoints

↓

Check Ingress

↓

Check ConfigMaps

↓

Check Secrets
```

---

# Useful Commands

View Pods

```bash
kubectl get pods
```

Watch Pods

```bash
kubectl get pods -w
```

Describe Pod

```bash
kubectl describe pod <pod-name>
```

Logs

```bash
kubectl logs <pod-name>
```

Follow Logs

```bash
kubectl logs -f <pod-name>
```

Execute into Container

```bash
kubectl exec -it <pod-name> -- /bin/sh
```

Port Forward

```bash
kubectl port-forward svc/my-service 8080:80
```

Resource Usage

```bash
kubectl top pods
```

```bash
kubectl top nodes
```

View Events

```bash
kubectl get events
```

View Services

```bash
kubectl get svc
```

View Endpoints

```bash
kubectl get endpoints
```

View Ingress

```bash
kubectl get ingress
```

---

# Real Troubleshooting Examples

## ImagePullBackOff

Check:

```bash
kubectl describe pod
```

Look for:

```
Failed to pull image
```

Verify:

- Image exists
- Correct tag
- Registry credentials

---

## CrashLoopBackOff

Check:

```bash
kubectl logs <pod-name> --previous
```

Review:

- Application exceptions
- Missing environment variables
- Startup command

---

## Pending Pod

Check:

```bash
kubectl describe pod
```

Possible messages:

```
Insufficient CPU

Insufficient Memory

Untolerated Taint

PVC Pending
```

---

## Service Not Working

Check:

```bash
kubectl get endpoints
```

If empty:

- Verify Service selector
- Verify Pod labels
- Ensure Pods are Ready

---

## High CPU

Use:

```bash
kubectl top pods
```

Then inspect:

```bash
kubectl logs
```

and consider:

- Resource limits
- Infinite loops
- Heavy workloads

---

# Best Practices

✅ Monitor clusters continuously with Prometheus.

✅ Build dashboards in Grafana.

✅ Configure alerts with Alertmanager.

✅ Add liveness, readiness, and startup probes.

✅ Set CPU and Memory requests/limits.

✅ Centralize logs (for example, using Fluent Bit/Fluentd with Elasticsearch/OpenSearch or Loki).

---

# Common Mistakes

❌ Relying only on `kubectl get pods`.

❌ Ignoring Kubernetes Events.

❌ Deploying applications without probes.

❌ Not collecting historical metrics.

❌ Not monitoring node health.

---

# Interview Questions

## Beginner

1. What is monitoring?
2. Difference between monitoring and logging?
3. What is Prometheus?
4. What is Grafana?
5. What is Metrics Server?

---

## Intermediate

6. How does Prometheus collect metrics?
7. What is kube-state-metrics?
8. Difference between Node Exporter and kube-state-metrics?
9. Explain liveness and readiness probes.
10. What causes CrashLoopBackOff?

---

## Advanced

11. Explain the complete Kubernetes monitoring architecture.
12. How would you troubleshoot a Pending Pod?
13. How would you investigate an ImagePullBackOff error?
14. Why are readiness probes important for rolling updates?
15. How would you design a production monitoring and alerting stack?

---

# 📝 Quick Revision

- Observability = Metrics + Logs + Traces.
- Metrics Server provides current CPU/Memory usage for `kubectl top` and HPA.
- Prometheus scrapes and stores metrics.
- kube-state-metrics exposes Kubernetes object state.
- Node Exporter exposes node OS metrics.
- Grafana visualizes metrics from Prometheus.
- `kubectl logs` shows container logs.
- `kubectl describe` is the first command for deep troubleshooting.
- Events explain Kubernetes actions.
- Liveness restarts unhealthy containers.
- Readiness controls whether traffic reaches a Pod.
- Startup probes support slow-starting applications.
- Follow a systematic troubleshooting workflow instead of guessing.
