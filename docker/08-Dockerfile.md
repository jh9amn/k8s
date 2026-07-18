# 🐳 Chapter 8 - Dockerfile

> **"A Dockerfile is a blueprint that defines how to build a Docker Image. Every instruction creates a reproducible and automated build process."**

---

# 📖 Table of Contents

1. What is a Dockerfile?
2. Why Use Dockerfiles?
3. Docker Build Process
4. Dockerfile Syntax
5. Dockerfile Instructions
6. Build Context
7. Docker Build Cache
8. .dockerignore
9. Building Images
10. Running Images
11. Dockerfile Best Practices
12. Common Mistakes
13. Hands-on Labs
14. Interview Questions
15. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Write Dockerfiles from scratch
- Understand each Dockerfile instruction
- Build custom Docker images
- Optimize image size
- Use Docker build cache effectively
- Follow production-ready best practices

---

# 1. What is a Dockerfile?

A **Dockerfile** is a plain text file containing a series of instructions that Docker uses to build an image.

Think of it as a recipe.

```text
Dockerfile
      │
      ▼
docker build
      │
      ▼
Docker Image
      │
      ▼
Container
```

---

# 2. Why Use Dockerfiles?

Without Dockerfiles:

- Manual installation
- Inconsistent environments
- Difficult deployments
- Hard to reproduce builds

With Dockerfiles:

- Automated builds
- Version-controlled infrastructure
- Repeatable deployments
- CI/CD friendly

---

# 3. Docker Build Process

```text
Dockerfile
      │
      ▼
Docker CLI
      │
      ▼
Docker Daemon
      │
      ▼
Image Layers
      │
      ▼
Docker Image
```

Each instruction generally creates a new layer.

---

# 4. Dockerfile Syntax

Example:

```dockerfile
FROM ubuntu:24.04

RUN apt update

RUN apt install -y nginx

COPY . /app

WORKDIR /app

CMD ["nginx","-g","daemon off;"]
```

---

# 5. Dockerfile Instructions

## FROM

Specifies the base image.

```dockerfile
FROM ubuntu:24.04
```

Every Dockerfile starts with a `FROM` instruction (unless you're creating an image `FROM scratch`).

---

## LABEL

Adds metadata.

```dockerfile
LABEL maintainer="aman@example.com"

LABEL version="1.0"

LABEL project="Docker Handbook"
```

Inspect labels:

```bash
docker inspect image_name
```

---

## WORKDIR

Sets the working directory.

```dockerfile
WORKDIR /app
```

Equivalent to:

```bash
cd /app
```

If the directory doesn't exist, Docker creates it.

---

## COPY

Copies files from the build context into the image.

```dockerfile
COPY . .
```

Copy a single file:

```dockerfile
COPY app.py /app/
```

Preferred over `ADD` for local files.

---

## ADD

Copies files and supports additional features.

```dockerfile
ADD app.tar.gz /app/
```

It can:

- Copy local files
- Extract local tar archives automatically
- Download from URLs (not generally recommended)

For most use cases, prefer `COPY`.

---

## RUN

Executes commands while building the image.

```dockerfile
RUN apt update

RUN apt install -y nginx
```

Better:

```dockerfile
RUN apt update && \
    apt install -y nginx && \
    apt clean
```

Combining commands reduces image layers and keeps images smaller.

---

## ENV

Sets environment variables.

```dockerfile
ENV APP_ENV=production

ENV PORT=8080
```

Read inside the container:

```bash
echo $APP_ENV
```

---

## EXPOSE

Documents which port the application listens on.

```dockerfile
EXPOSE 80
```

> **Note:** `EXPOSE` does **not** publish the port to the host. You still need `docker run -p`.

---

## USER

Runs the application as a non-root user.

```dockerfile
RUN useradd appuser

USER appuser
```

This improves container security.

---

## CMD

Defines the default command.

```dockerfile
CMD ["nginx","-g","daemon off;"]
```

Only one `CMD` should exist in a Dockerfile. If multiple are specified, the last one takes effect.

---

## ENTRYPOINT

Defines the main executable.

```dockerfile
ENTRYPOINT ["python3"]
```

With:

```dockerfile
CMD ["app.py"]
```

Running:

```bash
docker run myapp
```

Executes:

```bash
python3 app.py
```

---

# CMD vs ENTRYPOINT

| CMD | ENTRYPOINT |
|------|------------|
| Default command | Main executable |
| Easily overridden | Usually fixed |
| Optional | Commonly used for application containers |

---

# 6. Build Context

The **build context** is the directory sent to the Docker daemon during `docker build`.

Example:

```text
project/

├── Dockerfile
├── app.py
├── requirements.txt
└── static/
```

Build:

```bash
docker build -t myapp .
```

The final `.` represents the build context.

---

# 7. Docker Build Cache

Docker caches layers to speed up builds.

Example:

```dockerfile
FROM ubuntu

RUN apt update

COPY . .
```

If only application files change, Docker can reuse the cached `FROM` and `RUN` layers.

---

## Cache Optimization

Less frequently changed instructions should appear first.

Good:

```dockerfile
FROM python:3.12

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .
```

This avoids reinstalling dependencies when only source code changes.

---

# 8. .dockerignore

Similar to `.gitignore`.

Example:

```text
.git

node_modules

.env

__pycache__

*.log

*.tmp
```

Benefits:

- Faster builds
- Smaller build context
- Better security
- Reduced image size

---

# 9. Building Images

Build:

```bash
docker build -t myapp:v1 .
```

Build without cache:

```bash
docker build --no-cache -t myapp:v1 .
```

Build using a different Dockerfile:

```bash
docker build -f Dockerfile.dev -t myapp:dev .
```

---

# 10. Running the Image

Run:

```bash
docker run myapp:v1
```

Run in detached mode:

```bash
docker run -d myapp:v1
```

Publish a port:

```bash
docker run -p 8080:80 myapp:v1
```

---

# Example: Python Application

**app.py**

```python
print("Hello Docker!")
```

**Dockerfile**

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY app.py .

CMD ["python3", "app.py"]
```

Build:

```bash
docker build -t hello-python .
```

Run:

```bash
docker run hello-python
```

Output:

```text
Hello Docker!
```

---

# Example: Node.js Application

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

---

# Example: Java Spring Boot

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```

---

# 11. Dockerfile Best Practices

- Use official base images.
- Pin image versions instead of `latest`.
- Use minimal base images (`alpine`, `slim`) where appropriate.
- Combine `RUN` instructions when possible.
- Clean package caches after installation.
- Use `.dockerignore`.
- Avoid copying unnecessary files.
- Run applications as a non-root user.
- Keep images small and focused.
- Use multi-stage builds (covered in a later chapter).

---

# 12. Common Mistakes

❌ Using `latest` in production.

❌ Running as `root`.

❌ Copying the entire project before installing dependencies.

❌ Leaving package caches in the image.

❌ Forgetting `.dockerignore`.

❌ Embedding secrets (passwords, API keys) in the Dockerfile.

---

# Hands-on Labs

## Lab 1

Create a Dockerfile for a Python application.

---

## Lab 2

Build the image.

---

## Lab 3

Run the container.

---

## Lab 4

Add environment variables.

---

## Lab 5

Optimize the Dockerfile using build cache.

---

## Lab 6

Create a `.dockerignore` file.

---

# Interview Questions

## Beginner

- What is a Dockerfile?
- What is the purpose of `FROM`?
- Difference between `COPY` and `ADD`?

---

## Intermediate

- Explain `CMD` vs `ENTRYPOINT`.
- What is the Docker build context?
- How does Docker build cache work?

---

## Advanced

- How would you optimize a Dockerfile?
- Why should applications avoid running as `root`?
- How can poor instruction ordering affect build performance?

---

# Chapter Summary

In this chapter, you learned:

- Dockerfile fundamentals
- Dockerfile instructions
- Build context
- Docker build cache
- `.dockerignore`
- Building and running images
- Production best practices
- Common mistakes
