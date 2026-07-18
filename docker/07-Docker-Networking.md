# 🌐 Chapter 7 - Docker Networking

> **"Networking enables containers to communicate with each other, the host system, and external services. Docker provides multiple network drivers for different deployment scenarios."**

---

# 📖 Table of Contents

1. Introduction to Docker Networking
2. Why Networking is Required
3. Docker Network Architecture
4. Docker Network Drivers
5. Bridge Network
6. Host Network
7. None Network
8. Overlay Network
9. Macvlan Network
10. Port Mapping
11. Exposing Ports
12. Container Communication
13. Docker DNS
14. Creating Custom Networks
15. Network Inspection
16. Network Troubleshooting
17. Best Practices
18. Hands-on Labs
19. Interview Questions
20. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Docker networking concepts
- Explain Docker network drivers
- Connect containers together
- Expose container services
- Troubleshoot networking issues
- Design production-ready container networks

---

# 1. Introduction to Docker Networking

Containers are isolated by default.

Without networking:

- Containers cannot communicate.
- Services cannot be accessed from outside.
- Microservices cannot interact.

Docker networking solves these problems.

---

# 2. Why Networking is Required

Imagine a web application:

```text
Browser
    │
    ▼
Nginx
    │
    ▼
Backend API
    │
    ▼
Database
```

Each component runs in a separate container.

Networking allows them to communicate securely.

---

# 3. Docker Network Architecture

```text
             Host Machine
+------------------------------------+
|                                    |
|  Docker Engine                     |
|                                    |
|   +----------------------------+   |
|   | Docker Network             |   |
|   +----------------------------+   |
|      ▲             ▲              |
|      │             │              |
|  Container A   Container B        |
|                                    |
+------------------------------------+
```

The Docker Engine manages networking between containers.

---

# 4. Docker Network Drivers

Docker provides several built-in network drivers.

| Driver | Description | Common Use |
|--------|-------------|------------|
| Bridge | Default local network | Single-host containers |
| Host | Shares host network | High-performance networking |
| None | No networking | Security testing |
| Overlay | Multi-host networking | Docker Swarm |
| Macvlan | Container gets its own MAC/IP | Legacy applications |

List available networks:

```bash
docker network ls
```

---

# 5. Bridge Network

The **bridge** network is Docker's default network.

```text
             Bridge Network
        +----------------------+
        |                      |
Container A              Container B
        |                      |
        +----------+-----------+
                   │
              Host Machine
```

Create a bridge network:

```bash
docker network create my-bridge
```

Run containers on it:

```bash
docker run -d --name app1 --network my-bridge nginx

docker run -d --name app2 --network my-bridge ubuntu
```

Containers on the same bridge network can communicate using their names.

---

# 6. Host Network

In host mode, the container shares the host's network stack.

```text
Container
     │
     ▼
Host Network
```

Run:

```bash
docker run --network host nginx
```

Advantages:

- Better performance
- No Network Address Translation (NAT)

Limitations:

- Less isolation
- Linux only

---

# 7. None Network

The container has no network access.

```bash
docker run --network none ubuntu
```

Use cases:

- Security testing
- Offline processing
- Batch jobs

---

# 8. Overlay Network

Overlay networks connect containers running on different Docker hosts.

```text
Host A                 Host B
+---------+           +---------+
| App A   |-----------| App B   |
+---------+           +---------+
       Overlay Network
```

Typically used with Docker Swarm.

---

# 9. Macvlan Network

A Macvlan network assigns a unique MAC address and IP address to each container.

```text
Router
   │
   ├──────── Container A
   ├──────── Container B
   └──────── Laptop
```

Useful for:

- Legacy applications
- Network appliances
- Systems requiring direct network visibility

---

# 10. Port Mapping

Containers are isolated from the host.

Port mapping exposes container services.

Syntax:

```bash
docker run -p HOST_PORT:CONTAINER_PORT nginx
```

Example:

```bash
docker run -d -p 8080:80 nginx
```

Access:

```text
http://localhost:8080
```

Flow:

```text
Browser
    │
localhost:8080
    │
Docker Engine
    │
Container:80
```

---

# 11. Exposing Ports

Expose port 80:

```bash
docker run -p 8080:80 nginx
```

Expose multiple ports:

```bash
docker run \
-p 8080:80 \
-p 8443:443 \
nginx
```

Random host port:

```bash
docker run -P nginx
```

View mappings:

```bash
docker port <container_name>
```

---

# 12. Container Communication

Run two containers:

```bash
docker network create app-net
```

```bash
docker run -d \
--name backend \
--network app-net \
nginx
```

```bash
docker run -it \
--network app-net \
ubuntu
```

Inside the Ubuntu container:

```bash
ping backend
```

Docker automatically resolves the hostname.

---

# 13. Docker DNS

Every custom Docker network includes an internal DNS server.

```text
frontend
      │
DNS Lookup
      │
backend
```

Instead of using IP addresses:

```text
172.18.0.3
```

Use container names:

```text
backend
```

Advantages:

- Easier maintenance
- Containers can be recreated without changing configuration
- Better support for microservices

---

# 14. Creating Custom Networks

Create:

```bash
docker network create my-network
```

Attach a container:

```bash
docker run \
--network my-network \
nginx
```

Connect an existing container:

```bash
docker network connect my-network web
```

Disconnect:

```bash
docker network disconnect my-network web
```

---

# 15. Network Inspection

List networks:

```bash
docker network ls
```

Inspect:

```bash
docker network inspect bridge
```

Inspect custom network:

```bash
docker network inspect my-network
```

Useful information:

- Subnet
- Gateway
- Connected containers
- Driver
- Labels

---

# 16. Network Troubleshooting

List networks:

```bash
docker network ls
```

Check container IP:

```bash
docker inspect web
```

Ping another container:

```bash
ping backend
```

Test connectivity:

```bash
curl http://backend
```

View mapped ports:

```bash
docker port web
```

Inspect network:

```bash
docker network inspect app-net
```

---

# 17. Best Practices

- Use custom bridge networks for applications.
- Avoid relying on container IP addresses.
- Use container names or service names.
- Expose only required ports.
- Isolate unrelated applications into separate networks.
- Prefer bridge networks for single-host deployments.
- Use overlay networks for multi-host deployments.
- Follow the principle of least privilege for network exposure.

---

# Hands-on Labs

## Lab 1

Create a custom bridge network.

---

## Lab 2

Run two containers on the same network.

---

## Lab 3

Verify communication using:

```bash
ping
```

---

## Lab 4

Run an Nginx container and expose port 8080.

Access it in your browser.

---

## Lab 5

Inspect a Docker network.

---

## Lab 6

Disconnect and reconnect a container to a network.

---

# Interview Questions

## Beginner

- What is Docker networking?
- What is the default Docker network?
- What is port mapping?

---

## Intermediate

- Difference between Bridge and Host networks?
- What is Overlay networking?
- Why use custom bridge networks?
- How does Docker DNS work?

---

## Advanced

- Explain Docker's networking architecture.
- When would you choose Macvlan?
- How do containers on different hosts communicate?
- How would you troubleshoot a container networking issue?

---

# Chapter Summary

In this chapter, you learned:

- Docker networking fundamentals
- Network drivers
- Bridge, Host, None, Overlay, and Macvlan networks
- Port mapping
- Exposing services
- Container-to-container communication
- Docker DNS
- Custom networks
- Network inspection
- Networking best practices
