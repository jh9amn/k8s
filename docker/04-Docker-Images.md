# 🐳 Chapter 4 - Docker Images

> **"A Docker Image is a lightweight, immutable blueprint used to create containers. Understanding images is the foundation of mastering Docker."**

---

# 📖 Table of Contents

1. What is a Docker Image?
2. Image vs Container
3. Docker Image Architecture
4. Image Layers
5. Union File System (UnionFS)
6. OverlayFS
7. Docker Image Lifecycle
8. Docker Hub
9. Image Naming Convention
10. Pulling Images
11. Listing Images
12. Inspecting Images
13. Tagging Images
14. Removing Images
15. Saving & Loading Images
16. Image History
17. Image Optimization
18. Best Practices
19. Hands-on Labs
20. Interview Questions
21. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Docker Images
- Explain image layers
- Understand OverlayFS
- Pull images from Docker Hub
- Tag and manage images
- Optimize image size
- Inspect image metadata

---

# 1. What is a Docker Image?

A **Docker Image** is an **immutable (read-only) template** used to create Docker containers.

It contains everything needed to run an application:

- Application code
- Runtime
- Libraries
- Dependencies
- Configuration files
- Environment variables

Think of it as a **blueprint**.

```text
Docker Image
      │
      ├────────► Container 1
      ├────────► Container 2
      └────────► Container 3
```

One image can create multiple containers.

---

# Real World Analogy

Think of an image as a **cake recipe**.

The recipe never changes.

Every time you bake a cake:

Recipe → Cake

Docker follows the same principle.

Image → Container

---

# 2. Image vs Container

| Docker Image | Docker Container |
|--------------|------------------|
| Read-only | Writable |
| Template | Running instance |
| Immutable | Mutable |
| Stored on disk | Running in memory |
| Can create many containers | Runs one application |

---

# Example

Pull Ubuntu image

```bash
docker pull ubuntu
```

Create two containers

```bash
docker run ubuntu

docker run ubuntu
```

Result

```text
Ubuntu Image

   │

 ├────► Container A

 └────► Container B
```

---

# 3. Docker Image Architecture

Each Docker image consists of multiple **layers**.

```text
+---------------------------+
| Application Layer         |
+---------------------------+
| Dependencies              |
+---------------------------+
| Runtime                   |
+---------------------------+
| Base Image                |
+---------------------------+
```

Each instruction in a Dockerfile generally creates a new layer.

---

# 4. Image Layers

Docker images are layered.

Example Dockerfile

```dockerfile
FROM ubuntu

RUN apt update

RUN apt install nginx

COPY . /app

CMD ["nginx","-g","daemon off;"]
```

Generated layers

```text
Layer 5 → CMD

Layer 4 → COPY

Layer 3 → Install Nginx

Layer 2 → apt update

Layer 1 → Ubuntu Base Image
```

Benefits:

- Faster builds
- Efficient caching
- Smaller downloads
- Layer reuse

---

# Layer Reuse

Suppose two images use Ubuntu.

```text
Image A

Ubuntu Layer

Python Layer

Application A

-------------------

Image B

Ubuntu Layer

Node Layer

Application B
```

Ubuntu layer is stored only once.

---

# 5. Union File System (UnionFS)

Docker combines multiple read-only layers into one unified filesystem.

```text
Application Layer

Dependencies

Runtime

Ubuntu

↓

Merged View

↓

Container
```

The container sees a single filesystem.

---

# 6. OverlayFS

Docker uses **OverlayFS** on most Linux systems.

OverlayFS merges:

- Lower Layers (Read-only)
- Upper Layer (Writable)

```text
Upper Layer (Writable)

───────────────

Lower Layer

Lower Layer

Lower Layer

───────────────

Merged Filesystem
```

Changes made by a running container go only to the writable layer.

The original image never changes.

---

# Copy-on-Write (CoW)

Docker uses **Copy-on-Write**.

Example

Container starts

↓

Reads file

↓

No copy needed

↓

Modify file

↓

Docker copies only that file to writable layer

Advantages

- Faster
- Less storage
- Better performance

---

# 7. Docker Image Lifecycle

```text
Dockerfile

      │

docker build

      │

Docker Image

      │

docker push

      │

Docker Registry

      │

docker pull

      │

Run Container
```

---

# 8. Docker Hub

Docker Hub is the default public registry.

Contains:

- Official Images
- Verified Publisher Images
- Community Images

Examples

```bash
docker pull nginx

docker pull redis

docker pull mysql

docker pull ubuntu
```

---

# Official Images

Official images are maintained by Docker and trusted maintainers.

Examples

- nginx
- ubuntu
- mysql
- postgres
- redis
- node
- python

---

# 9. Image Naming Convention

```text
repository:tag
```

Example

```text
nginx:latest

ubuntu:24.04

node:22

python:3.12
```

If no tag is provided:

Docker uses

```text
latest
```

---

# 10. Pulling Images

Pull latest image

```bash
docker pull nginx
```

Pull a specific version

```bash
docker pull ubuntu:24.04
```

Pull Python

```bash
docker pull python:3.12
```

---

# 11. Listing Images

List local images

```bash
docker images
```

or

```bash
docker image ls
```

Example

```text
REPOSITORY   TAG       IMAGE ID

ubuntu       24.04     a12b34

nginx        latest    c34d56
```

---

# 12. Inspecting Images

Display metadata

```bash
docker image inspect nginx
```

Useful information

- Image ID
- Created time
- OS
- Architecture
- Layers
- Entrypoint
- Environment variables

---

# 13. Tagging Images

Rename image

```bash
docker tag nginx my-nginx:v1
```

Verify

```bash
docker images
```

---

# 14. Removing Images

Remove one image

```bash
docker rmi nginx
```

Remove multiple images

```bash
docker rmi ubuntu nginx redis
```

Remove unused images

```bash
docker image prune
```

Remove all unused images

```bash
docker image prune -a
```

---

# 15. Saving & Loading Images

Save image

```bash
docker save -o nginx.tar nginx
```

Load image

```bash
docker load -i nginx.tar
```

Useful when transferring images to systems without internet access.

---

# 16. Image History

View layers

```bash
docker history nginx
```

Shows

- Layer size
- Commands
- Creation time

---

# 17. Image Optimization

Large images take longer to download and deploy.

Tips:

- Use minimal base images
- Combine RUN commands
- Remove package cache
- Use `.dockerignore`
- Use Multi-stage Builds (covered later)
- Pin image versions instead of always using `latest`

---

# Good vs Bad

❌ Bad

```dockerfile
FROM ubuntu
```

Large image.

---

✅ Better

```dockerfile
FROM alpine
```

Very small image.

---

# 18. Best Practices

- Use official images
- Avoid `latest` in production
- Remove unused images
- Scan images for vulnerabilities
- Keep images updated
- Minimize image size
- Use multi-stage builds
- Pin image versions

---

# Hands-on Labs

## Lab 1

Pull:

```bash
ubuntu

nginx

redis
```

---

## Lab 2

List all images.

---

## Lab 3

Inspect an image.

---

## Lab 4

Tag an image.

---

## Lab 5

Save and reload an image.

---

## Lab 6

View image history.

---

# Interview Questions

### Beginner

- What is a Docker Image?
- Difference between image and container?
- What is Docker Hub?
- What is an image tag?

### Intermediate

- Explain image layers.
- What is OverlayFS?
- What is Copy-on-Write?
- How does Docker cache layers?

### Advanced

- Why are Docker images immutable?
- Explain UnionFS.
- How can you reduce image size?
- Why should `latest` be avoided in production?

---

# Chapter Summary

In this chapter, you learned:

- Docker Images
- Image Layers
- UnionFS
- OverlayFS
- Copy-on-Write
- Docker Hub
- Image Tags
- Pulling Images
- Inspecting Images
- Saving & Loading Images
- Image Optimization
- Best Practices
