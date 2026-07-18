# 🐳 Chapter 10 - Docker Registry

> **"A Docker Registry is a centralized repository for storing, versioning, and distributing Docker Images across development, testing, and production environments."**

---

# 📖 Table of Contents

1. What is a Docker Registry?
2. Why Do We Need a Registry?
3. Docker Registry Architecture
4. Docker Hub
5. Private Registries
6. Image Naming Convention
7. Image Tagging Strategy
8. Logging In to a Registry
9. Pushing Images
10. Pulling Images
11. Docker Content Trust
12. Registry Security
13. Best Practices
14. Hands-on Labs
15. Interview Questions
16. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Docker Registries
- Push and pull images
- Work with Docker Hub
- Configure private registries
- Apply image tagging strategies
- Secure container images

---

# 1. What is a Docker Registry?

A **Docker Registry** is a service that stores Docker Images.

Instead of building images on every machine, developers build an image once and store it in a registry.

Other systems can then download (pull) the image.

```text
Developer
     │
docker build
     │
     ▼
Docker Image
     │
docker push
     ▼
Docker Registry
     │
docker pull
     ▼
Production Server
```

---

# 2. Why Do We Need a Registry?

Imagine a team of developers.

Without a registry:

- Everyone builds images separately.
- Different versions may exist.
- Deployments become inconsistent.

With a registry:

- One trusted image
- Version control
- Easy distribution
- Faster deployments

---

# 3. Docker Registry Architecture

```text
             Docker Registry
+------------------------------------+
|                                    |
| Repository                         |
|    ├── Image:v1                    |
|    ├── Image:v2                    |
|    └── Image:latest                |
+------------------------------------+
            ▲               ▲
            │               │
     docker push      docker pull
            │               │
      Developer        Production
```

---

# 4. Docker Hub

Docker Hub is Docker's default public registry.

Website:

```text
https://hub.docker.com
```

Features:

- Official Images
- Public repositories
- Private repositories
- Automated builds
- Image versioning

Popular images:

```text
nginx

ubuntu

redis

mysql

postgres

python

node
```

---

# 5. Private Registries

Organizations often use private registries to store proprietary images.

Common options:

- Docker Hub (Private Repositories)
- GitHub Container Registry (GHCR)
- Amazon Elastic Container Registry (ECR)
- Google Artifact Registry
- Azure Container Registry (ACR)
- Self-hosted Docker Registry

Benefits:

- Better security
- Access control
- Internal image distribution

---

# 6. Image Naming Convention

Image format:

```text
registry/repository:tag
```

Examples:

Docker Hub:

```text
nginx:latest
```

Personal repository:

```text
aman/myapp:v1
```

GitHub Container Registry:

```text
ghcr.io/company/myapp:v1
```

Amazon ECR:

```text
123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp:v1
```

---

# 7. Image Tagging Strategy

Tags identify image versions.

Example:

```text
myapp:v1.0

myapp:v1.1

myapp:v2.0
```

Avoid relying on:

```text
latest
```

in production.

Recommended strategies:

- Semantic Versioning (`v1.2.0`)
- Git commit SHA
- Build number
- Release date

---

# 8. Logging In to a Registry

Log in to Docker Hub:

```bash
docker login
```

You'll be prompted for:

- Username
- Password or Personal Access Token (recommended)

Log out:

```bash
docker logout
```

---

# 9. Pushing Images

Tag an image:

```bash
docker tag myapp:v1 yourusername/myapp:v1
```

Push the image:

```bash
docker push yourusername/myapp:v1
```

Workflow:

```text
Build Image
      │
      ▼
Tag Image
      │
      ▼
docker push
      │
      ▼
Docker Registry
```

---

# 10. Pulling Images

Download an image:

```bash
docker pull nginx
```

Pull a specific version:

```bash
docker pull python:3.12-slim
```

Pull from a private registry:

```bash
docker pull yourusername/myapp:v1
```

List local images:

```bash
docker images
```

---

# 11. Docker Content Trust

Docker Content Trust (DCT) helps verify that an image has not been tampered with.

Enable DCT:

```bash
export DOCKER_CONTENT_TRUST=1
```

Benefits:

- Image integrity
- Publisher verification
- Reduced supply chain risk

> **Note:** Docker Content Trust is a legacy feature. Many organizations now use tools such as **Sigstore/Cosign** or **Notary v2** for signing and verifying container images.

---

# 12. Registry Security

Follow these practices:

- Use private repositories for sensitive images.
- Enable Multi-Factor Authentication (MFA).
- Scan images for vulnerabilities.
- Avoid embedding secrets in images.
- Remove unused images.
- Keep base images updated.
- Sign and verify images where possible.

---

# Registry vs Repository

| Registry | Repository |
|----------|------------|
| Stores many repositories | Stores related image versions |
| Example: Docker Hub | Example: `yourusername/myapp` |

---

# Docker Image Lifecycle

```text
Write Dockerfile
       │
       ▼
docker build
       │
       ▼
Docker Image
       │
docker tag
       ▼
Tagged Image
       │
docker push
       ▼
Registry
       │
docker pull
       ▼
Deployment
```

---

# Best Practices

- Use semantic version tags.
- Avoid `latest` in production.
- Use official base images.
- Scan images regularly.
- Remove obsolete image tags.
- Restrict registry access using least privilege.
- Automate image publishing through CI/CD.

---

# Hands-on Labs

## Lab 1

Create a Docker Hub account (if you don't already have one).

---

## Lab 2

Build a custom image.

---

## Lab 3

Tag the image.

---

## Lab 4

Push the image to Docker Hub.

---

## Lab 5

Delete the local image and pull it again from the registry.

---

## Lab 6

Explore the tags available for an official image such as `nginx`.

---

# Interview Questions

## Beginner

- What is a Docker Registry?
- What is Docker Hub?
- What is an image tag?

---

## Intermediate

- Difference between a Registry and a Repository?
- How do you push an image?
- Why should you avoid `latest` in production?

---

## Advanced

- Explain an enterprise image publishing workflow.
- How would you secure a private registry?
- How can image signing improve software supply chain security?

---

# Chapter Summary

In this chapter, you learned:

- Docker Registries
- Docker Hub
- Private Registries
- Image naming and tagging
- Logging in
- Pushing and pulling images
- Docker Content Trust
- Registry security
- Production best practices
