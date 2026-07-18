# 🏗️ Chapter 2 - Docker Architecture

> **"To use Docker effectively, you should understand what happens behind every `docker` command."**

---

# 📖 Table of Contents

1. Docker Architecture Overview
2. Client-Server Architecture
3. Docker Engine
4. Docker Daemon (dockerd)
5. Docker Client (CLI)
6. Docker REST API
7. Docker Objects
8. Docker Images
9. Docker Containers
10. Docker Registry
11. Docker Workflow
12. Docker Lifecycle
13. Hands-on Labs
14. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Docker's client-server architecture
- Explain the role of Docker Engine
- Describe how Docker CLI communicates with Docker Daemon
- Differentiate images and containers
- Understand Docker Registry
- Explain the complete Docker workflow

---

# 1. Docker Architecture Overview

Docker follows a **Client-Server Architecture**.

```text
                User
                  │
                  ▼
          Docker CLI (Client)
                  │
          Docker REST API
                  │
                  ▼
        Docker Daemon (dockerd)
                  │
        ┌─────────┼─────────┐
        ▼         ▼         ▼
     Images   Containers  Networks
                  │
                  ▼
             Host Operating System
                  │
                  ▼
               Hardware
```

The Docker CLI sends commands to the Docker Daemon, which performs all container-related operations.

---

# 2. Client-Server Architecture

Docker is divided into two main parts:

## Docker Client

Used by users to interact with Docker.

Example:

```bash
docker run nginx
```

---

## Docker Server

The Docker Daemon (`dockerd`) receives the command and performs the requested action.

---

## Communication Flow

```text
docker run nginx
        │
        ▼
Docker CLI
        │
        ▼
Docker REST API
        │
        ▼
Docker Daemon
        │
        ▼
Pull Image (if needed)
        │
        ▼
Create Container
        │
        ▼
Run Container
```

---

# 3. Docker Engine

Docker Engine is the core software that enables containerization.

It consists of:

- Docker Daemon
- Docker REST API
- Docker CLI

```text
Docker Engine
     │
 ┌───┼──────────┐
 ▼   ▼          ▼
CLI API     Daemon
```

Responsibilities:

- Build images
- Run containers
- Manage networks
- Manage volumes
- Pull and push images
- Monitor container lifecycle

---

# 4. Docker Daemon (dockerd)

The Docker Daemon is a background service responsible for managing Docker objects.

It:

- Builds images
- Starts containers
- Stops containers
- Creates networks
- Creates volumes
- Downloads images
- Removes unused resources

Check its status:

```bash
systemctl status docker
```

On most Linux distributions, the Docker daemon starts automatically after installation.

---

# 5. Docker Client (CLI)

The Docker CLI is the command-line interface used to communicate with Docker.

Examples:

```bash
docker ps

docker images

docker run ubuntu

docker stop container_name
```

The CLI does not create containers itself—it simply sends requests to the Docker Daemon.

---

# 6. Docker REST API

The Docker CLI communicates with the daemon through a REST API.

```text
Docker CLI
      │
HTTP Request
      │
      ▼
Docker REST API
      │
      ▼
Docker Daemon
```

This API allows third-party tools and applications to manage Docker programmatically.

---

# 7. Docker Objects

Docker manages several types of objects.

| Object | Purpose |
|---------|---------|
| Image | Blueprint for containers |
| Container | Running instance of an image |
| Volume | Persistent storage |
| Network | Communication between containers |
| Registry | Stores Docker images |

---

# 8. Docker Images

A Docker Image is a **read-only template** used to create containers.

Think of it like a class in object-oriented programming.

Example:

```text
Docker Image
      │
      ├──► Container A
      ├──► Container B
      └──► Container C
```

Multiple containers can be created from the same image.

---

## Image Layers

Docker images are built using layers.

```text
+-----------------------+
| Application Layer     |
+-----------------------+
| Dependencies          |
+-----------------------+
| Runtime               |
+-----------------------+
| Base OS               |
+-----------------------+
```

Benefits:

- Reusability
- Faster builds
- Smaller downloads
- Layer caching

---

# 9. Docker Containers

A container is a **running instance of an image**.

Unlike an image, a container is writable while it is running.

Example:

```text
Image
   │
   ├──► Container 1
   ├──► Container 2
   └──► Container 3
```

Each container has:

- Unique ID
- IP Address
- Filesystem
- Network interface
- Process space

---

## Container States

```text
Created
    │
    ▼
Running
    │
    ▼
Paused
    │
    ▼
Stopped
    │
    ▼
Removed
```

---

# 10. Docker Registry

A Docker Registry stores Docker images.

Types:

- Public Registry
- Private Registry

Examples:

- Docker Hub
- GitHub Container Registry (GHCR)
- Amazon Elastic Container Registry (ECR)
- Google Artifact Registry

---

## Image Pull Process

```text
docker pull nginx
        │
        ▼
Docker Hub
        │
        ▼
Download Image
        │
        ▼
Local Image Cache
```

If the image already exists locally, Docker reuses it instead of downloading it again.

---

# 11. Docker Workflow

A typical Docker workflow:

```text
Write Application
        │
        ▼
Write Dockerfile
        │
        ▼
Build Image
        │
        ▼
Store Image
        │
        ▼
Run Container
        │
        ▼
Push to Registry
        │
        ▼
Deploy to Server
```

---

# 12. Docker Lifecycle

The lifecycle of a container:

```text
Pull Image
     │
     ▼
Create Container
     │
     ▼
Start Container
     │
     ▼
Running
     │
     ▼
Stop
     │
     ▼
Restart
     │
     ▼
Remove
```

---

# Hands-on Labs

## Lab 1

Draw the Docker architecture and label:

- Docker CLI
- Docker Daemon
- Docker Engine
- Images
- Containers
- Registry

---

## Lab 2

Explain the difference between:

- Image
- Container
- Registry
- Docker Engine

---

## Lab 3

Describe what happens internally when you execute:

```bash
docker run nginx
```

---

# Chapter Summary

In this chapter, you learned:

- Docker Client-Server Architecture
- Docker Engine
- Docker Daemon
- Docker CLI
- Docker REST API
- Docker Objects
- Docker Images
- Docker Containers
- Docker Registry
- Docker Workflow
- Container Lifecycle
