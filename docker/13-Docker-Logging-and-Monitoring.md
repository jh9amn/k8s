# 📊 Chapter 13 - Docker Logging and Monitoring

> **"You can't manage what you can't observe. Logging and monitoring provide visibility into container behavior, application health, and system performance."**

---

# 📖 Table of Contents

1. Introduction to Logging & Monitoring
2. Docker Logging Architecture
3. Docker Logging Drivers
4. Viewing Container Logs
5. Log Rotation
6. Docker Events
7. Docker Stats
8. Health Checks
9. Monitoring with Prometheus
10. Monitoring with Grafana
11. Centralized Logging
12. Best Practices
13. Hands-on Labs
14. Interview Questions
15. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Docker logging
- Configure logging drivers
- Monitor running containers
- Perform health checks
- Integrate Prometheus & Grafana
- Implement production monitoring practices

---

# 1. Introduction to Logging & Monitoring

Observability consists of:

- **Logs** → What happened?
- **Metrics** → How is the system performing?
- **Traces** → Where is the request spending time?

Docker provides built-in support for logs and metrics.

---

# 2. Docker Logging Architecture

```text
Application
      │
stdout / stderr
      │
      ▼
Docker Engine
      │
      ▼
Logging Driver
      │
      ▼
Storage / External System
```

Applications should write logs to **stdout** and **stderr** instead of local files.

---

# 3. Docker Logging Drivers

Docker supports multiple logging drivers.

| Driver | Description |
|---------|-------------|
| json-file | Default driver, stores logs in JSON format |
| local | Efficient local storage with built-in rotation |
| syslog | Sends logs to Syslog |
| journald | Uses systemd journal |
| fluentd | Sends logs to Fluentd |
| gelf | Sends logs to Graylog |
| awslogs | Sends logs to Amazon CloudWatch |
| splunk | Sends logs to Splunk |

Check the default logging driver:

```bash
docker info
```

Run a container with a specific logging driver:

```bash
docker run \
--log-driver=json-file \
nginx
```

---

# 4. Viewing Container Logs

View logs:

```bash
docker logs web
```

Follow logs:

```bash
docker logs -f web
```

Last 20 lines:

```bash
docker logs --tail 20 web
```

Include timestamps:

```bash
docker logs -t web
```

View logs since a specific duration:

```bash
docker logs --since 10m web
```

---

# 5. Log Rotation

Without rotation, log files may consume excessive disk space.

Example:

```bash
docker run \
--log-driver=json-file \
--log-opt max-size=10m \
--log-opt max-file=3 \
nginx
```

Behavior:

- Maximum log file size: **10 MB**
- Keep **3** rotated log files

---

# 6. Docker Events

Docker records lifecycle events.

View live events:

```bash
docker events
```

Example output:

```text
container start
container stop
container die
network connect
image pull
```

Useful for troubleshooting and automation.

---

# 7. Docker Stats

Monitor resource usage in real time:

```bash
docker stats
```

Example:

```text
CONTAINER   CPU %   MEM USAGE   NET I/O
web         2.5%    120MiB      3MB
db          6.8%    450MiB      12MB
```

Monitor a specific container:

```bash
docker stats web
```

---

# 8. Health Checks

Health checks determine whether an application is functioning correctly.

Dockerfile example:

```dockerfile
HEALTHCHECK \
CMD curl -f http://localhost || exit 1
```

Runtime example:

```bash
docker run \
--health-cmd="curl -f http://localhost || exit 1" \
nginx
```

Inspect health status:

```bash
docker inspect web
```

Possible states:

- `starting`
- `healthy`
- `unhealthy`

---

# 9. Monitoring with Prometheus

Prometheus collects metrics from exporters.

Architecture:

```text
Docker Containers
        │
        ▼
Docker Metrics Exporter
        │
        ▼
Prometheus
        │
        ▼
Grafana
```

Example:

```text
Prometheus

↓

Scrape Metrics

↓

Docker Engine

↓

Containers
```

---

# 10. Monitoring with Grafana

Grafana visualizes metrics.

Typical dashboards include:

- CPU usage
- Memory usage
- Network traffic
- Disk I/O
- Running containers
- Restart count

```text
Prometheus

↓

Metrics

↓

Grafana Dashboard
```

---

# 11. Centralized Logging

Instead of storing logs on each host, send them to a centralized platform.

Common stacks:

| Tool | Purpose |
|------|---------|
| ELK (Elasticsearch, Logstash, Kibana) | Search and visualize logs |
| Loki + Grafana | Lightweight log aggregation |
| Fluentd | Log collection and forwarding |
| Graylog | Log management |
| Splunk | Enterprise log analysis |

Architecture:

```text
Containers
      │
      ▼
Logging Driver
      │
      ▼
Fluentd / Logstash
      │
      ▼
Storage
      │
      ▼
Grafana / Kibana
```

---

# 12. Best Practices

- Write application logs to `stdout` and `stderr`.
- Configure log rotation.
- Use centralized logging in production.
- Monitor CPU, memory, disk, and network usage.
- Enable health checks for all services.
- Set alerts for unhealthy containers.
- Keep logs structured (e.g., JSON) for easier searching.
- Retain logs according to organizational policies.

---

# Hands-on Labs

## Lab 1

Run an Nginx container and view its logs.

```bash
docker logs <container_name>
```

---

## Lab 2

Follow logs in real time.

```bash
docker logs -f <container_name>
```

---

## Lab 3

Run a container with log rotation enabled.

---

## Lab 4

Monitor resource usage with:

```bash
docker stats
```

---

## Lab 5

Inspect Docker events.

```bash
docker events
```

---

## Lab 6

Add a health check to a Dockerfile and verify the container's health status.

---

# Interview Questions

## Beginner

- How do you view Docker logs?
- What is the default logging driver?
- What does `docker stats` show?

---

## Intermediate

- Why is log rotation important?
- Explain Docker health checks.
- How do Docker logging drivers work?

---

## Advanced

- How would you implement centralized logging?
- Why should applications log to `stdout` instead of files?
- How would you monitor a production Docker environment?

---

# Chapter Summary

In this chapter, you learned:

- Docker logging architecture
- Logging drivers
- Viewing logs
- Log rotation
- Docker events
- Docker stats
- Health checks
- Monitoring with Prometheus
- Monitoring with Grafana
- Centralized logging
- Production monitoring best practices
