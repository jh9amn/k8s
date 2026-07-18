# 🔐 Chapter 12 - Docker Security

> **"Containers provide isolation, but security requires multiple layers of protection. Secure images, least privilege, runtime restrictions, and continuous scanning are essential for production deployments."**

---

# 📖 Table of Contents

1. Introduction to Docker Security
2. Docker Security Architecture
3. Linux Namespaces
4. Linux cgroups
5. Running Containers as Non-Root
6. Linux Capabilities
7. Seccomp Profiles
8. AppArmor & SELinux
9. Read-Only File Systems
10. Resource Limits
11. Secrets Management
12. Image Vulnerability Scanning
13. Docker Bench for Security
14. Image Signing
15. Security Best Practices
16. Hands-on Labs
17. Interview Questions
18. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Docker security architecture
- Explain Namespaces and cgroups
- Run containers securely
- Manage Linux capabilities
- Apply runtime security controls
- Scan container images
- Protect secrets
- Harden containers for production

---

# 1. Introduction to Docker Security

Docker security is based on multiple layers:

```text
Application
      │
Docker Image
      │
Container Runtime
      │
Linux Kernel
      │
Host Operating System
```

Security is only as strong as the weakest layer.

---

# 2. Docker Security Architecture

```text
+-----------------------------+
|      Application            |
+-----------------------------+
             │
+-----------------------------+
|      Docker Image           |
+-----------------------------+
             │
+-----------------------------+
|   Docker Engine             |
+-----------------------------+
             │
+-----------------------------+
| Linux Namespaces            |
| Linux cgroups               |
| Linux Capabilities          |
| Seccomp                     |
| AppArmor / SELinux          |
+-----------------------------+
             │
+-----------------------------+
| Linux Kernel                |
+-----------------------------+
```

---

# 3. Linux Namespaces

Namespaces isolate resources between containers.

Types:

| Namespace | Isolates |
|-----------|----------|
| PID | Processes |
| NET | Network interfaces |
| IPC | Inter-process communication |
| MNT | File system mounts |
| UTS | Hostname and domain name |
| USER | User and group IDs |
| CGROUP | cgroup view |

Example:

```text
Container A

PID 1

PID 2

-------------------

Container B

PID 1

PID 2
```

Each container has its own isolated process tree.

---

# 4. Linux cgroups

**Control Groups (cgroups)** limit and monitor resource usage.

They control:

- CPU
- Memory
- Disk I/O
- Network bandwidth
- Number of processes

Example:

```bash
docker run \
--cpus="2" \
-m 1g \
nginx
```

This limits the container to:

- 2 CPUs
- 1 GB RAM

---

# 5. Running Containers as Non-Root

Avoid running applications as the root user.

❌

```dockerfile
FROM ubuntu

CMD ["nginx"]
```

✅

```dockerfile
FROM ubuntu

RUN useradd appuser

USER appuser

CMD ["nginx"]
```

Benefits:

- Reduced privilege
- Smaller attack surface
- Better isolation

---

# 6. Linux Capabilities

Linux divides root privileges into smaller capabilities.

Examples:

- `CAP_NET_ADMIN`
- `CAP_SYS_ADMIN`
- `CAP_CHOWN`

Drop unnecessary capabilities:

```bash
docker run \
--cap-drop ALL \
nginx
```

Add only what is required:

```bash
docker run \
--cap-drop ALL \
--cap-add NET_BIND_SERVICE \
nginx
```

---

# 7. Seccomp Profiles

**Seccomp** filters Linux system calls.

Docker applies a default seccomp profile that blocks many potentially dangerous system calls.

Use the default profile:

```bash
docker run nginx
```

Use a custom profile:

```bash
docker run \
--security-opt seccomp=profile.json \
nginx
```

Benefits:

- Reduces kernel attack surface
- Restricts unnecessary system calls

---

# 8. AppArmor & SELinux

These Linux Security Modules enforce mandatory access control.

## AppArmor

Restricts:

- File access
- Capabilities
- Networking
- Mount operations

Example:

```bash
docker run \
--security-opt apparmor=docker-default \
nginx
```

---

## SELinux

Provides policy-based access control.

Example:

```bash
docker run \
-v /data:/data:Z \
nginx
```

The `:Z` option relabels the directory for container access on SELinux-enabled systems.

---

# 9. Read-Only File Systems

Prevent modifications inside the container filesystem.

```bash
docker run \
--read-only \
nginx
```

Benefits:

- Prevents malware persistence
- Protects application files
- Reduces accidental changes

If writable locations are needed, mount volumes or tmpfs:

```bash
docker run \
--read-only \
--tmpfs /tmp \
nginx
```

---

# 10. Resource Limits

Limit memory:

```bash
docker run \
-m 512m \
nginx
```

Limit CPUs:

```bash
docker run \
--cpus="1" \
nginx
```

Limit process count:

```bash
docker run \
--pids-limit 100 \
nginx
```

---

# 11. Secrets Management

Never hardcode secrets.

❌

```dockerfile
ENV DB_PASSWORD=admin123
```

Better options:

- Docker Secrets (Swarm)
- Kubernetes Secrets
- Vault
- Cloud secret managers
- Environment variables injected by CI/CD

Example:

```bash
docker run \
-e DB_PASSWORD=secret \
myapp
```

---

# 12. Image Vulnerability Scanning

Regularly scan images for known vulnerabilities.

Common tools:

- Docker Scout
- Trivy
- Grype
- Snyk

Example:

```bash
docker scout quickview myapp
```

Or with Trivy:

```bash
trivy image myapp
```

---

# 13. Docker Bench for Security

Docker Bench is an automated script that checks Docker against security best practices.

Run:

```bash
docker run --rm \
--net host \
--pid host \
--userns host \
--cap-add audit_control \
-v /var/lib:/var/lib \
-v /var/run/docker.sock:/var/run/docker.sock \
docker/docker-bench-security
```

It reports:

- Configuration issues
- Insecure permissions
- Missing recommendations

---

# 14. Image Signing

Signing verifies that an image has not been tampered with.

Modern options include:

- Sigstore Cosign
- Notary v2 (emerging)

Benefits:

- Verify publisher identity
- Protect software supply chains
- Increase deployment trust

---

# 15. Security Best Practices

- Use official base images.
- Keep base images updated.
- Avoid `latest` tags.
- Run containers as non-root.
- Remove unnecessary Linux capabilities.
- Enable read-only filesystems where possible.
- Limit CPU, memory, and process usage.
- Scan images regularly.
- Never store secrets inside images.
- Sign production images.
- Keep Docker Engine updated.

---

# Hands-on Labs

## Lab 1

Run a container as a non-root user.

---

## Lab 2

Limit CPU and memory usage.

---

## Lab 3

Run a container with a read-only filesystem.

---

## Lab 4

Drop all Linux capabilities and add back only the required ones.

---

## Lab 5

Scan an image using Docker Scout or Trivy.

---

## Lab 6

Review your Dockerfiles for embedded secrets.

---

# Interview Questions

## Beginner

- Why shouldn't containers run as root?
- What are Linux namespaces?
- What are cgroups?

---

## Intermediate

- Explain Linux capabilities.
- What is Seccomp?
- How does AppArmor differ from SELinux?

---

## Advanced

- How would you harden a production container?
- How do you secure secrets in containerized applications?
- What steps would you include in a container security pipeline?

---

# Chapter Summary

In this chapter, you learned:

- Docker security architecture
- Linux Namespaces
- cgroups
- Non-root containers
- Linux capabilities
- Seccomp
- AppArmor & SELinux
- Read-only filesystems
- Resource limits
- Secrets management
- Vulnerability scanning
- Image signing
- Production security best practices
