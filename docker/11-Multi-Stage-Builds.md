# 🐳 Chapter 11 - Multi-Stage Builds

> **"Multi-stage builds separate the build process from the runtime environment, producing smaller, cleaner, and more secure Docker images."**

---

# 📖 Table of Contents

1. What are Multi-Stage Builds?
2. Why Use Multi-Stage Builds?
3. Single-Stage vs Multi-Stage Builds
4. Build Stage vs Runtime Stage
5. How Multi-Stage Builds Work
6. Multi-Stage Build Examples
7. BuildKit
8. Optimizing Docker Images
9. Best Practices
10. Hands-on Labs
11. Interview Questions
12. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Multi-Stage Builds
- Reduce Docker image size
- Improve image security
- Separate build and runtime dependencies
- Use BuildKit for faster builds

---

# 1. What are Multi-Stage Builds?

A **Multi-Stage Build** uses **multiple `FROM` instructions** in a single Dockerfile.

Each stage has a specific purpose:

- Build the application
- Package dependencies
- Create the final runtime image

Only the required artifacts are copied into the final image.

---

# 2. Why Use Multi-Stage Builds?

Consider a Java application.

To compile it, you need:

- JDK
- Maven
- Source code

To run it, you only need:

- JRE
- Compiled JAR

Without Multi-Stage Builds, the final image includes everything.

With Multi-Stage Builds, the final image contains only the runtime components.

Benefits:

- Smaller images
- Faster downloads
- Improved security
- Reduced attack surface
- Faster deployments

---

# 3. Single-Stage vs Multi-Stage Builds

## Single-Stage Build

```dockerfile
FROM maven:3.9-eclipse-temurin-21

WORKDIR /app

COPY . .

RUN mvn package

CMD ["java","-jar","target/app.jar"]
```

Final image contains:

- Source code
- Maven
- Build cache
- JDK
- Compiled JAR

---

## Multi-Stage Build

```dockerfile
# Build Stage
FROM maven:3.9-eclipse-temurin-21 AS builder

WORKDIR /app

COPY . .

RUN mvn clean package

# Runtime Stage
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=builder /app/target/app.jar .

ENTRYPOINT ["java","-jar","app.jar"]
```

Final image contains only:

- JRE
- Compiled application

---

# 4. Build Stage vs Runtime Stage

```text
          Build Stage
+------------------------------+
| Source Code                  |
| Maven / Gradle / npm         |
| Build Tools                  |
| Compile Application          |
+------------------------------+
               │
               ▼
     Copy Build Artifacts
               │
               ▼
         Runtime Stage
+------------------------------+
| Runtime Only                 |
| Application                  |
| Minimal Dependencies         |
+------------------------------+
```

---

# 5. How Multi-Stage Builds Work

```text
Dockerfile
      │
      ▼
Stage 1
(Build Application)
      │
      ▼
Compiled Artifact
      │
COPY --from
      ▼
Stage 2
(Runtime Image)
```

Docker discards the intermediate stages after the final image is built.

---

# 6. Multi-Stage Build Examples

## Example: Node.js

```dockerfile
# Build Stage
FROM node:22 AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

# Runtime Stage
FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx","-g","daemon off;"]
```

---

## Example: Go

```dockerfile
# Build Stage
FROM golang:1.23 AS builder

WORKDIR /src

COPY . .

RUN go build -o app

# Runtime Stage
FROM alpine:3.20

WORKDIR /app

COPY --from=builder /src/app .

ENTRYPOINT ["./app"]
```

---

## Example: Python

```dockerfile
# Build Stage
FROM python:3.12 AS builder

WORKDIR /app

COPY requirements.txt .

RUN pip install --prefix=/install -r requirements.txt

COPY . .

# Runtime Stage
FROM python:3.12-slim

WORKDIR /app

COPY --from=builder /install /usr/local

COPY --from=builder /app .

CMD ["python","app.py"]
```

---

# 7. BuildKit

BuildKit is Docker's modern build engine.

Advantages:

- Faster builds
- Parallel execution
- Improved caching
- Secret management
- SSH forwarding

Enable BuildKit:

```bash
export DOCKER_BUILDKIT=1
```

Build:

```bash
docker build -t myapp .
```

---

# 8. Optimizing Docker Images

## Use Minimal Base Images

❌

```dockerfile
FROM ubuntu
```

✅

```dockerfile
FROM alpine
```

or

```dockerfile
FROM python:3.12-slim
```

---

## Combine RUN Commands

❌

```dockerfile
RUN apt update

RUN apt install curl
```

✅

```dockerfile
RUN apt update && \
    apt install -y curl && \
    rm -rf /var/lib/apt/lists/*
```

---

## Copy Dependencies First

```dockerfile
COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .
```

This maximizes Docker's build cache.

---

## Use .dockerignore

Example:

```text
.git
node_modules
.env
*.log
__pycache__
```

---

# Image Size Comparison

| Build Type | Approximate Size |
|------------|-----------------:|
| Single-Stage Java | 700 MB |
| Multi-Stage Java | 220 MB |
| Single-Stage Node.js | 350 MB |
| Multi-Stage Node.js | 60 MB |

> Actual sizes vary depending on the application and base images.

---

# 9. Best Practices

- Use Multi-Stage Builds for compiled applications.
- Use minimal runtime images.
- Avoid copying unnecessary files.
- Leverage BuildKit.
- Pin base image versions.
- Remove package caches.
- Run applications as a non-root user.
- Scan final images for vulnerabilities.

---

# Hands-on Labs

## Lab 1

Convert a single-stage Dockerfile into a multi-stage build.

---

## Lab 2

Compare image sizes before and after optimization.

---

## Lab 3

Enable BuildKit and build the image.

---

## Lab 4

Use `.dockerignore` to reduce build context.

---

## Lab 5

Optimize a Node.js or Java application using multi-stage builds.

---

# Interview Questions

## Beginner

- What is a Multi-Stage Build?
- Why use multiple `FROM` instructions?

---

## Intermediate

- How does `COPY --from` work?
- Why are Multi-Stage Builds more secure?

---

## Advanced

- How would you optimize a production Docker image?
- How does BuildKit improve the build process?
- Why should build tools not exist in runtime images?

---

# Chapter Summary

In this chapter, you learned:

- Multi-Stage Builds
- Build Stage vs Runtime Stage
- `COPY --from`
- BuildKit
- Image optimization
- Production best practices
