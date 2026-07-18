# 🐳 Chapter 10 - Docker

> **"Build once, run anywhere."**

Docker changed the way software is developed, tested, and deployed by packaging applications together with all their dependencies into lightweight, portable containers.

Today Docker is one of the core technologies behind:

- DevOps
- Cloud Computing
- Microservices
- Kubernetes
- CI/CD
- AI/ML Deployments

---

# 📖 Table of Contents

1. What is Docker?
2. Why Docker?
3. Problems Before Docker
4. Virtual Machines vs Containers
5. What is a Container?
6. Docker Architecture
7. Docker Components
8. Installing Docker
9. Docker Workflow
10. Your First Container
11. Basic Docker Commands
12. Docker Objects
13. Docker Lifecycle
14. Docker Internals (Introduction)
15. Hands-on Lab

---

# 🎯 Learning Objectives

After this chapter you'll understand:

- Why Docker exists
- Containers vs Virtual Machines
- Docker architecture
- Docker Engine
- Images
- Containers
- Registries
- Basic Docker commands
- Docker lifecycle
- How Docker fits into Kubernetes

---

# 1. What is Docker?

Docker is an **open-source containerization platform**.

It packages:

- Application
- Runtime
- Libraries
- Dependencies
- Configuration

into a single portable unit called a **container**.

Instead of saying:

> "It works on my machine."

Docker lets you say:

> "It works everywhere Docker runs."

---

# Traditional Deployment Problem

Imagine a Python application.

It requires:

- Python 3.12
- FastAPI
- Pandas
- NumPy
- Uvicorn

Developer Laptop

```
Works ✅
```

QA Server

```
Python version mismatch ❌
```

Production

```
Missing dependency ❌
```

Result:

```
Application fails.
```

---

# Docker Solution

Package everything together.

```
Application
Python
Libraries
Dependencies
Configurations

↓

Docker Image

↓

Run Anywhere
```

The same image runs consistently on:

- Windows
- Linux
- macOS
- Cloud VMs
- Kubernetes
- CI/CD pipelines

---

# 2. Why Docker?

Docker solves many common software delivery problems.

### Portability

The same container behaves consistently across environments.

### Isolation

Each application has its own filesystem, libraries, and runtime.

### Fast Startup

Containers typically start in seconds because they share the host kernel.

### Lightweight

Containers avoid running a full guest operating system.

### Scalability

Running multiple identical containers is simple.

### Reproducibility

Images define environments as code.

---

# 3. Problems Before Docker

Before containers, developers often faced:

- Dependency conflicts
- Different operating systems
- Library version mismatches
- Manual installations
- Difficult deployments

Example:

Application A

```
Python 3.8
```

Application B

```
Python 3.12
```

Installing both globally often caused conflicts.

Docker isolates each application.

---

# 4. Virtual Machines vs Containers

## Virtual Machine

```
+------------------------+
| Application            |
+------------------------+
| Guest Operating System |
+------------------------+
| Hypervisor             |
+------------------------+
| Host Operating System  |
+------------------------+
| Hardware               |
+------------------------+
```

Each VM contains its own operating system.

---

## Container

```
+------------------------+
| Application            |
+------------------------+
| Libraries              |
+------------------------+
| Docker Engine          |
+------------------------+
| Host Operating System  |
+------------------------+
| Hardware               |
+------------------------+
```

Containers share the host kernel.

---

## Comparison

| Feature | VM | Container |
|----------|----|-----------|
| Guest OS | Required | Not Required |
| Startup | Minutes | Seconds |
| Size | GBs | MBs |
| Isolation | Strong | Process-level |
| Performance | Higher overhead | Near native |
| Density | Lower | Higher |

---

# Why Containers are Faster

VM boot sequence:

```
Power On

↓

BIOS

↓

Kernel

↓

Operating System

↓

Application
```

Container:

```
Host Kernel

↓

Container Process
```

No separate OS boot is required.

---

# 5. What is a Container?

A container is simply an **isolated process** running on the host.

It has its own:

- Filesystem
- Network namespace
- Process namespace
- Environment variables
- Users
- Mounted volumes

But it shares the host Linux kernel.

---

# Real-Life Analogy

Think of an apartment building.

```
Building
```

↓

Host Operating System

Each Apartment

↓

Container

Every apartment has:

- Kitchen
- Bedroom
- Furniture

But all apartments share:

- Foundation
- Electricity
- Water

Similarly, containers share the kernel while keeping application environments separate.

---

# 6. Docker Architecture

```
+----------------------+
| Docker CLI           |
+----------+-----------+
           |
           v
+----------------------+
| Docker Engine        |
| (dockerd)            |
+----------+-----------+
           |
           v
+----------------------+
| containerd           |
+----------+-----------+
           |
           v
+----------------------+
| runc                 |
+----------+-----------+
           |
           v
+----------------------+
| Linux Kernel         |
+----------------------+
```

---

## Docker CLI

Command-line interface.

Example:

```bash
docker ps
docker images
docker run nginx
```

---

## Docker Daemon (`dockerd`)

The background service that:

- Builds images
- Runs containers
- Manages networks
- Manages volumes
- Communicates with registries

---

## containerd

Responsible for:

- Image management
- Container lifecycle
- Storage
- Runtime management

Docker uses `containerd` under the hood.

---

## runc

A low-level runtime that actually creates Linux containers using kernel features.

---

# 7. Docker Components

Docker consists of:

## Docker Engine

Runs containers.

---

## Docker Image

Read-only template.

Example:

```
ubuntu:24.04

python:3.12

nginx:latest
```

---

## Docker Container

Running instance of an image.

```
Image

↓

Container
```

---

## Docker Registry

Stores Docker images.

Examples:

- Docker Hub
- GitHub Container Registry
- Amazon ECR
- Google Artifact Registry
- Azure Container Registry

---

# 8. Installing Docker

Ubuntu:

```bash
sudo apt update

sudo apt install docker.io
```

Check version:

```bash
docker --version
```

Check daemon:

```bash
systemctl status docker
```

---

# 9. Docker Workflow

```
Dockerfile

↓

docker build

↓

Docker Image

↓

docker run

↓

Container
```

This is the fundamental Docker workflow.

---

# 10. Your First Container

Run:

```bash
docker run hello-world
```

Docker performs the following:

1. Checks local image cache.
2. If not found, downloads the image from Docker Hub.
3. Creates a writable container layer.
4. Starts the container.
5. Executes the default command.
6. Displays the output.
7. Stops the container.

---

# 11. Basic Docker Commands

Check Docker version:

```bash
docker --version
```

---

Display Docker information:

```bash
docker info
```

---

List images:

```bash
docker images
```

---

List running containers:

```bash
docker ps
```

---

List all containers:

```bash
docker ps -a
```

---

Run a container:

```bash
docker run nginx
```

---

Run interactively:

```bash
docker run -it ubuntu bash
```

---

Remove a container:

```bash
docker rm CONTAINER_ID
```

---

Remove an image:

```bash
docker rmi IMAGE_ID
```

---

# 12. Docker Objects

Docker manages several object types:

- Images
- Containers
- Volumes
- Networks
- Build Cache

Each object has its own lifecycle and management commands.

---

# 13. Docker Lifecycle

```
Dockerfile
      │
      ▼
docker build
      │
      ▼
Docker Image
      │
      ▼
docker run
      │
      ▼
Container Created
      │
      ▼
Container Running
      │
      ▼
Stopped
      │
      ▼
Removed
```

---

# 14. Docker Internals (Introduction)

Docker relies on Linux kernel features:

- Namespaces → Isolation
- cgroups → Resource limits
- OverlayFS → Layered filesystem
- Union mounts → Efficient image storage

We'll explore these in detail later in this chapter.

---

# 15. Hands-on Lab

Verify Docker:

```bash
docker --version
docker info
```

Pull an image:

```bash
docker pull nginx
```

Run a container:

```bash
docker run nginx
```

List containers:

```bash
docker ps
docker ps -a
```

Stop a container:

```bash
docker stop CONTAINER_ID
```

Remove the container:

```bash
docker rm CONTAINER_ID
```

Remove the image:

```bash
docker rmi nginx
```

---

# Learning Summary

In this part, you learned:

- Why Docker exists
- Problems it solves
- Containers vs Virtual Machines
- Docker architecture
- Docker Engine
- Images
- Containers
- Registries
- Docker workflow
- Basic Docker commands

---

---

# 16. Docker Images

A **Docker Image** is a **read-only template** used to create containers.

Think of it like:

```
Class  ----------> Object
Docker Image ---> Container
```

One image can create hundreds of containers.

Example:

```
nginx:latest

↓

docker run nginx

↓

Container 1

↓

docker run nginx

↓

Container 2
```

Both containers come from the same image.

---

## Image Structure

A Docker image consists of multiple **layers**.

Example:

```
Application Layer
-----------------
Python Packages
-----------------
Ubuntu Base Image
-----------------
Linux Kernel (Host)
```

Each layer is immutable.

---

# 17. Image Layers

Docker images are built layer by layer.

Example Dockerfile:

```dockerfile
FROM ubuntu

RUN apt update

RUN apt install python3

COPY . .

CMD ["python3","app.py"]
```

Generated Layers:

```
Layer 5 -> CMD

Layer 4 -> COPY

Layer 3 -> apt install

Layer 2 -> apt update

Layer 1 -> Ubuntu Base
```

Every instruction creates a new image layer.

---

## Why Layers?

Layers provide:

- Faster builds
- Less storage usage
- Shared caching
- Easier distribution

---

# Layer Reuse

Suppose two projects use:

```
FROM ubuntu
```

Docker downloads Ubuntu only once.

Project A

```
Ubuntu
Python
App A
```

Project B

```
Ubuntu
Node
App B
```

Ubuntu layer is shared.

---

# 18. Dockerfile

A Dockerfile is a text file containing instructions to build an image.

Example:

```dockerfile
FROM python:3.12

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

CMD ["python","app.py"]
```

Build:

```bash
docker build -t myapp .
```

---

# Dockerfile Instructions

| Instruction | Purpose |
|------------|----------|
| FROM | Base image |
| LABEL | Metadata |
| WORKDIR | Working directory |
| COPY | Copy files |
| ADD | Copy + URL/Archive extraction |
| RUN | Execute during build |
| ENV | Environment variables |
| EXPOSE | Documentation for container port |
| CMD | Default command |
| ENTRYPOINT | Main executable |
| USER | Run as specific user |
| ARG | Build-time variables |
| VOLUME | Mount point |
| HEALTHCHECK | Health monitoring |

---

# 19. FROM

Every Dockerfile begins with a base image.

Example:

```dockerfile
FROM ubuntu:24.04
```

or

```dockerfile
FROM python:3.12
```

---

# 20. WORKDIR

Sets the current working directory.

```dockerfile
WORKDIR /app
```

Equivalent to:

```bash
cd /app
```

inside the container.

---

# 21. COPY

Copies files from your machine into the image.

```dockerfile
COPY . .
```

Example:

```
Host

app.py

↓

Container

/app/app.py
```

---

# COPY vs ADD

COPY

- Copies local files

ADD

- Copies files
- Downloads URLs
- Extracts archives automatically

Most projects should prefer **COPY**.

---

# 22. RUN

Executes commands while building the image.

Example:

```dockerfile
RUN apt update
```

Install packages:

```dockerfile
RUN apt install -y curl
```

Every RUN creates a new image layer.

---

# 23. CMD

Defines the default command.

Example:

```dockerfile
CMD ["python","app.py"]
```

Only one CMD should exist.

If multiple CMD instructions are present, the last one wins.

---

# CMD vs RUN

RUN

```
During Image Build
```

CMD

```
When Container Starts
```

Easy way to remember:

```
RUN = Build Time

CMD = Run Time
```

---

# 24. ENTRYPOINT

Defines the main executable.

Example:

```dockerfile
ENTRYPOINT ["python"]
```

CMD provides default arguments:

```dockerfile
CMD ["app.py"]
```

Result:

```
python app.py
```

---

# CMD vs ENTRYPOINT

| CMD | ENTRYPOINT |
|------|------------|
| Default command | Fixed executable |
| Can be overridden easily | Usually always runs |

Many production images use both.

---

# 25. ENV

Sets environment variables.

```dockerfile
ENV APP_ENV=production
```

Inside container:

```bash
echo $APP_ENV
```

---

# 26. ARG

Build-time variable.

```dockerfile
ARG VERSION=1.0
```

Build:

```bash
docker build --build-arg VERSION=2.0 .
```

Difference:

| ARG | ENV |
|------|-----|
| Build Time | Runtime |
| Not persisted | Persisted |

---

# 27. EXPOSE

Documents which port the application uses.

```dockerfile
EXPOSE 8000
```

Important:

```
EXPOSE

≠

Publish Port
```

Publishing is done with:

```bash
docker run -p 8000:8000 image
```

---

# 28. LABEL

Metadata.

```dockerfile
LABEL author="Aman"
```

Useful for:

- Owner
- Version
- Description
- License

---

# 29. USER

Avoid running containers as root.

```dockerfile
USER appuser
```

Production best practice.

---

# 30. HEALTHCHECK

Checks whether a container is healthy.

Example:

```dockerfile
HEALTHCHECK CMD curl --fail http://localhost:8000 || exit 1
```

Docker periodically runs the command.

---

# 31. .dockerignore

Similar to `.gitignore`.

Prevents unnecessary files from being sent to the Docker daemon.

Example:

```
.git

node_modules

venv

__pycache__

.env
```

Benefits:

- Faster builds
- Smaller context
- Better security

---

# 32. Build Context

When you run:

```bash
docker build .
```

Docker sends the **entire current directory** to the Docker daemon.

Large folders slow builds.

Always use:

```
.dockerignore
```

---

# 33. Docker Build Cache

Docker caches each layer.

Suppose:

```dockerfile
FROM python

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .
```

If only `app.py` changes:

```
FROM

Cached

↓

COPY requirements

Cached

↓

pip install

Cached

↓

COPY app.py

Rebuilt
```

Much faster.

---

# Cache Optimization

Bad:

```dockerfile
COPY . .

RUN pip install
```

Good:

```dockerfile
COPY requirements.txt .

RUN pip install

COPY . .
```

Dependencies change less often than application code.

---

# 34. Multi-stage Builds

Build image:

```dockerfile
FROM golang AS builder
```

Compile.

Then:

```dockerfile
FROM alpine

COPY --from=builder /app .
```

Final image contains only the compiled application.

Advantages:

- Smaller image
- Better security
- Faster deployments

---

# 35. Image Inspection

Inspect image:

```bash
docker inspect nginx
```

History:

```bash
docker history nginx
```

View layers:

```bash
docker image inspect nginx
```

---

# 36. Container Lifecycle

```
Created

↓

Running

↓

Paused

↓

Stopped

↓

Removed
```

Commands:

```bash
docker start

docker stop

docker restart

docker pause

docker unpause

docker rm
```

---

# 37. Useful Container Commands

Run:

```bash
docker run nginx
```

Detached:

```bash
docker run -d nginx
```

Interactive:

```bash
docker run -it ubuntu bash
```

Execute inside:

```bash
docker exec -it container bash
```

Logs:

```bash
docker logs container
```

Live logs:

```bash
docker logs -f container
```

Inspect:

```bash
docker inspect container
```

---

# 38. Hands-on Lab

Create:

```dockerfile
FROM python:3.12

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

EXPOSE 8000

CMD ["python","app.py"]
```

Build:

```bash
docker build -t fastapi-demo .
```

Run:

```bash
docker run -p 8000:8000 fastapi-demo
```

Verify:

```bash
docker ps

docker logs

docker inspect
```

---

# Learning Summary

You learned:

- Images
- Layers
- Dockerfile
- FROM
- COPY
- RUN
- CMD
- ENTRYPOINT
- ENV
- ARG
- EXPOSE
- LABEL
- USER
- HEALTHCHECK
- .dockerignore
- Build cache
- Multi-stage builds
- Container lifecycle
- Image inspection

---

---

# 39. Docker Volumes

Containers are **ephemeral**.

This means:

```
Container Deleted

↓

Data Inside Container Lost
```

To persist data, Docker provides **Volumes**.

```
Container

↓

Volume

↓

Host Storage
```

---

## Why Volumes?

Without a volume:

```
Container

↓

Database

↓

Container Deleted

↓

Database Lost ❌
```

With a volume:

```
Container

↓

Volume

↓

Database محفوظ ✅
```

(Stored on the host and survives container removal.)

---

## Create Volume

```bash
docker volume create mydata
```

List volumes:

```bash
docker volume ls
```

Inspect:

```bash
docker volume inspect mydata
```

Delete:

```bash
docker volume rm mydata
```

---

## Mount Volume

```bash
docker run \
-v mydata:/var/lib/mysql \
mysql
```

---

# 40. Bind Mounts

Bind mounts use an **existing directory** on the host.

```
Host Folder

↓

Container Folder
```

Example:

```bash
docker run \
-v $(pwd):/app \
python
```

Changes on the host appear immediately inside the container.

Great for development.

---

## Volume vs Bind Mount

| Volume | Bind Mount |
|---------|------------|
| Docker-managed | Host-managed |
| Better portability | Depends on host path |
| Recommended for databases | Recommended for development |

---

# 41. Docker Networking

Containers communicate through Docker networks.

Default network:

```
bridge
```

List networks:

```bash
docker network ls
```

Inspect:

```bash
docker network inspect bridge
```

---

## Network Types

| Network | Use Case |
|----------|----------|
| bridge | Default communication |
| host | Uses host network directly |
| none | No networking |
| overlay | Multi-host (Docker Swarm) |
| macvlan | Container gets its own MAC/IP |

---

## Bridge Network

```
Container A

↓

Bridge Network

↓

Container B
```

Create:

```bash
docker network create app-network
```

Run:

```bash
docker run \
--network app-network \
nginx
```

---

## Host Network

Container shares host network.

```bash
docker run \
--network host nginx
```

Useful for high-performance networking.

Linux only.

---

## None Network

```bash
docker run --network none ubuntu
```

Container has no network connectivity.

---

# 42. Port Mapping

Applications inside containers are isolated.

Expose to host:

```bash
docker run \
-p 8080:80 nginx
```

Meaning:

```
Host Port 8080

↓

Container Port 80
```

Multiple mappings:

```bash
-p 8080:80
-p 8443:443
```

---

# 43. Docker Compose

Docker Compose manages **multi-container applications**.

Instead of:

```bash
docker run ...

docker run ...

docker run ...
```

Use one file:

```
compose.yaml
```

---

## Example

```yaml
services:

  web:
    image: nginx

  redis:
    image: redis
```

Start:

```bash
docker compose up
```

Detached:

```bash
docker compose up -d
```

Stop:

```bash
docker compose down
```

---

## Why Compose?

Perfect for:

- Backend
- Database
- Redis
- Prometheus
- Grafana

running together.

---

# 44. Docker Registry

A registry stores Docker images.

Examples:

- Docker Hub
- GitHub Container Registry (GHCR)
- Amazon ECR
- Azure Container Registry
- Google Artifact Registry

Workflow:

```
Build

↓

Push

↓

Registry

↓

Pull

↓

Run
```

---

# 45. Docker Hub

Login:

```bash
docker login
```

Tag:

```bash
docker tag myapp username/myapp:v1
```

Push:

```bash
docker push username/myapp:v1
```

Pull:

```bash
docker pull username/myapp:v1
```

---

# 46. Docker Internals

Docker is built on Linux kernel features.

```
Namespaces

+

cgroups

+

OverlayFS

+

Union File System
```

---

## Namespaces

Provide isolation.

Types include:

- PID namespace
- Network namespace
- Mount namespace
- IPC namespace
- UTS namespace
- User namespace

Each container gets its own isolated view of system resources.

---

## cgroups (Control Groups)

Limit resource usage.

Examples:

- CPU
- Memory
- Disk I/O
- Network bandwidth

Example:

```bash
docker run --memory=512m nginx
```

CPU limit:

```bash
docker run --cpus=2 nginx
```

---

## OverlayFS

Docker images are made of read-only layers.

```
Layer 1

↓

Layer 2

↓

Layer 3

↓

Writable Layer
```

When a container starts, Docker adds a writable layer on top of the image.

Only this top layer changes.

---

# 47. Security Best Practices

✔ Use official images

✔ Keep images updated

✔ Run as non-root

```dockerfile
USER appuser
```

✔ Scan images

```bash
docker scout quickview myimage
```

(Or use your organization's preferred scanner.)

✔ Never store secrets in Dockerfiles.

Use environment variables or secret-management solutions instead.

✔ Keep images small.

---

# 48. Performance Best Practices

✔ Use Alpine or slim base images when appropriate.

✔ Multi-stage builds.

✔ Cache dependencies.

✔ Minimize layers.

Example:

Bad:

```dockerfile
RUN apt update

RUN apt install curl

RUN apt install git
```

Good:

```dockerfile
RUN apt update && \
    apt install -y curl git && \
    rm -rf /var/lib/apt/lists/*
```

---

# 49. Common Docker Problems

## Container Exits Immediately

Reason:

Main process finished.

Check:

```bash
docker logs container
```

---

## Port Already in Use

```
Bind failed
```

Solution:

Use another host port.

```bash
-p 8081:80
```

---

## Image Not Found

```
pull access denied
```

Possible causes:

- Wrong image name
- Private repository
- Not logged in

---

## Permission Denied

Linux:

```bash
sudo usermod -aG docker $USER
```

Log out and log back in.

---

## Container Cannot Reach Another Container

Check:

```bash
docker network inspect
```

Verify both containers are attached to the same network.

---

# 50. Docker vs Kubernetes

| Docker | Kubernetes |
|----------|------------|
| Builds images | Orchestrates containers |
| Runs containers | Runs Pods |
| Single host | Multi-node clusters |
| Manual scaling | Automatic scaling |
| Manual recovery | Self-healing |

Docker creates containers.

Kubernetes manages them at scale.

---

# 51. Interview Questions

### Beginner

- What is Docker?
- What is a container?
- Difference between image and container?
- Difference between VM and container?
- What is Docker Hub?

### Intermediate

- Explain Dockerfile instructions.
- Difference between CMD and ENTRYPOINT.
- COPY vs ADD.
- Volume vs Bind Mount.
- Bridge vs Host network.

### Advanced

- Explain image layers.
- What is OverlayFS?
- What are namespaces?
- What are cgroups?
- Explain build cache.
- What is a multi-stage build?
- How does Docker communicate with the Linux kernel?
- Why do containers start faster than VMs?

---

# 52. Docker Cheat Sheet

## Images

```bash
docker images
docker pull nginx
docker build -t app .
docker rmi IMAGE_ID
```

## Containers

```bash
docker ps
docker ps -a
docker run nginx
docker run -d nginx
docker stop ID
docker start ID
docker restart ID
docker rm ID
```

## Logs

```bash
docker logs ID
docker logs -f ID
```

## Exec

```bash
docker exec -it ID bash
```

## Networks

```bash
docker network ls
docker network create mynet
docker network inspect bridge
```

## Volumes

```bash
docker volume ls
docker volume create data
docker volume inspect data
```

## Compose

```bash
docker compose up -d
docker compose down
docker compose logs
```

## Cleanup

```bash
docker system df
docker system prune
docker image prune
docker volume prune
docker network prune
```

---

# 53. Practice Projects

1. Python Flask App
2. FastAPI Application
3. Node.js + MongoDB
4. Nginx Reverse Proxy
5. React + Backend + Database (Compose)
6. PostgreSQL with Volume
7. Redis Cache
8. Prometheus + Grafana Stack
9. Custom Docker Network
10. Multi-stage Build for Go or Java

---

# 54. Chapter Summary

You now understand:

- Docker architecture
- Images and containers
- Dockerfile instructions
- Layers and build cache
- Volumes and bind mounts
- Networking
- Docker Compose
- Registries and Docker Hub
- Namespaces, cgroups, OverlayFS
- Security and performance best practices
- Common troubleshooting
- Production-ready workflows
