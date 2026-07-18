# 🐳 Chapter 14 - Docker Best Practices

> **"Using Docker is easy. Using Docker correctly in production requires following proven best practices for security, performance, maintainability, and reliability."**

---

# 📖 Table of Contents

1. Introduction
2. Image Best Practices
3. Dockerfile Best Practices
4. Container Best Practices
5. Networking Best Practices
6. Storage Best Practices
7. Security Best Practices
8. Performance Optimization
9. CI/CD Best Practices
10. Production Deployment Checklist
11. Common Docker Anti-Patterns
12. Hands-on Labs
13. Interview Questions
14. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Build production-ready Docker images
- Write optimized Dockerfiles
- Secure containers
- Optimize performance
- Deploy Docker applications using industry best practices
- Avoid common mistakes

---

# 1. Introduction

Docker best practices help you create applications that are:

- Secure
- Portable
- Scalable
- Efficient
- Easy to maintain

Following these practices reduces deployment failures, security risks, and operational overhead.

---

# 2. Image Best Practices

## ✅ Use Official Images

Good:

```dockerfile
FROM nginx:1.28
```

Avoid:

```dockerfile
FROM random-user/nginx
```

---

## ✅ Pin Image Versions

Good:

```dockerfile
FROM python:3.12-slim
```

Avoid:

```dockerfile
FROM python:latest
```

Pinned versions ensure reproducible builds.

---

## ✅ Keep Images Small

Prefer lightweight base images:

- alpine
- slim
- distroless (where appropriate)

Benefits:

- Faster downloads
- Reduced attack surface
- Lower storage usage

---

## ✅ Remove Unnecessary Packages

Bad:

```dockerfile
RUN apt install vim
```

Good:

Install only what the application requires.

---

# 3. Dockerfile Best Practices

## Use Multi-Stage Builds

```dockerfile
FROM node:22 AS builder
# Build application

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

---

## Combine RUN Instructions

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

## Use .dockerignore

Example:

```text
.git
.env
node_modules
*.log
__pycache__
```

---

## Copy Dependency Files First

```dockerfile
COPY package*.json ./
RUN npm install
COPY . .
```

Improves build cache efficiency.

---

# 4. Container Best Practices

## Run One Main Process Per Container

Good:

- Nginx container
- MySQL container
- Redis container

Avoid running multiple unrelated services in one container.

---

## Use Read-Only Filesystems

```bash
docker run --read-only nginx
```

---

## Set Resource Limits

```bash
docker run \
--cpus=2 \
-m 1g \
myapp
```

---

## Use Restart Policies

```bash
docker run \
--restart unless-stopped \
myapp
```

Restart options:

- `no`
- `on-failure`
- `always`
- `unless-stopped`

---

# 5. Networking Best Practices

- Use custom bridge networks.
- Avoid the default bridge network for production applications.
- Expose only required ports.
- Use service names instead of IP addresses.
- Segment unrelated applications into separate networks.

Example:

```bash
docker network create app-network
```

---

# 6. Storage Best Practices

Use named volumes for persistent data.

Example:

```bash
docker volume create mysql-data
```

Mount the volume:

```bash
docker run \
-v mysql-data:/var/lib/mysql \
mysql
```

Avoid storing important data inside the container's writable layer.

Regularly back up production volumes.

---

# 7. Security Best Practices

- Run containers as a non-root user.
- Drop unnecessary Linux capabilities.
- Enable read-only filesystems where possible.
- Scan images regularly.
- Keep base images updated.
- Never hardcode passwords or API keys.
- Use signed images when available.
- Limit CPU, memory, and process usage.

Example:

```dockerfile
RUN useradd appuser
USER appuser
```

---

# 8. Performance Optimization

- Use minimal base images.
- Use Multi-Stage Builds.
- Optimize Dockerfile instruction order.
- Enable BuildKit.
- Use build cache effectively.
- Remove temporary files during builds.

Example:

```bash
export DOCKER_BUILDKIT=1
```

---

# 9. CI/CD Best Practices

Typical pipeline:

```text
Developer
      │
      ▼
Git Push
      │
      ▼
CI Pipeline
      │
      ▼
Run Tests
      │
      ▼
Build Docker Image
      │
      ▼
Scan Image
      │
      ▼
Push to Registry
      │
      ▼
Deploy
```

Recommendations:

- Automate image builds.
- Scan images before publishing.
- Use immutable image tags.
- Store Dockerfiles in version control.
- Automate deployments.

---

# 10. Production Deployment Checklist

Before deployment, verify:

- [ ] Base image is pinned to a specific version.
- [ ] `.dockerignore` is configured.
- [ ] Multi-stage build is used (where applicable).
- [ ] Image has been scanned for vulnerabilities.
- [ ] Container runs as a non-root user.
- [ ] Secrets are not embedded in the image.
- [ ] Resource limits are configured.
- [ ] Health checks are enabled.
- [ ] Logs are written to `stdout`/`stderr`.
- [ ] Named volumes are used for persistent data.
- [ ] Restart policy is configured.
- [ ] Monitoring and alerts are in place.

---

# 11. Common Docker Anti-Patterns

❌ Using the `latest` tag in production.

❌ Running containers as root.

❌ Installing unnecessary packages.

❌ Embedding secrets in Dockerfiles.

❌ Ignoring `.dockerignore`.

❌ Running multiple unrelated applications in one container.

❌ Storing persistent data inside the container filesystem.

❌ Not setting CPU and memory limits.

❌ Skipping vulnerability scans.

❌ Using large, outdated base images.

---

# Hands-on Labs

## Lab 1

Optimize an existing Dockerfile by combining `RUN` instructions.

---

## Lab 2

Convert a single-stage build into a multi-stage build.

---

## Lab 3

Run a container with:

- Non-root user
- Read-only filesystem
- CPU and memory limits

---

## Lab 4

Push a versioned image to a registry.

---

## Lab 5

Review an application against the production deployment checklist.

---

# Interview Questions

## Beginner

- Why should you avoid the `latest` tag?
- Why use `.dockerignore`?
- Why should containers run as non-root?

---

## Intermediate

- Explain the benefits of Multi-Stage Builds.
- How do you optimize Docker image size?
- Why should applications log to `stdout`?

---

## Advanced

- Design a production-ready Docker deployment process.
- How would you secure and monitor Docker containers?
- What are the most common Docker anti-patterns, and how would you avoid them?

---

# Chapter Summary

In this chapter, you learned:

- Image best practices
- Dockerfile optimization
- Secure container design
- Networking and storage recommendations
- Performance optimization
- CI/CD integration
- Production deployment checklist
- Common anti-patterns

---

# 🎉 Docker Handbook Complete!

Congratulations! You have completed the Docker section of your DevOps handbook.

You now understand:

- ✅ Docker Architecture
- ✅ Images & Containers
- ✅ Docker CLI
- ✅ Dockerfile
- ✅ Volumes
- ✅ Networking
- ✅ Docker Compose
- ✅ Docker Registry
- ✅ Multi-Stage Builds
- ✅ Security
- ✅ Logging & Monitoring
- ✅ Production Best Practices
