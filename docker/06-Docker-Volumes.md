# 🐳 Chapter 6 - Docker Volumes

> **"Containers are temporary, but your data doesn't have to be. Docker Volumes provide persistent storage that survives container restarts and recreation."**

---

# 📖 Table of Contents

1. Why Containers Lose Data
2. What is Persistent Storage?
3. Docker Storage Types
4. Docker Volumes
5. Bind Mounts
6. tmpfs Mounts
7. Volume Lifecycle
8. Managing Volumes
9. Sharing Volumes Between Containers
10. Backup & Restore Volumes
11. Volume Drivers
12. Best Practices
13. Hands-on Labs
14. Interview Questions
15. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Docker persistent storage
- Differentiate Volumes, Bind Mounts, and tmpfs
- Create and manage Docker Volumes
- Share data between containers
- Backup and restore volumes
- Apply storage best practices

---

# 1. Why Containers Lose Data

A container has a **writable layer** that exists only while the container exists.

```text
Docker Image
      │
      ▼
Container
      │
Writable Layer
```

If the container is removed:

```bash
docker rm my-container
```

The writable layer is deleted, and all data stored there is lost.

---

## Example

```bash
docker run -it ubuntu
```

Create a file:

```bash
echo "Hello Docker" > data.txt
```

Exit and remove the container:

```bash
docker rm <container_id>
```

Create a new container from the same image:

```bash
docker run -it ubuntu
```

The file `data.txt` no longer exists.

---

# 2. What is Persistent Storage?

Persistent storage ensures that data remains available even after:

- Container restart
- Container recreation
- Image updates

Examples of applications requiring persistent storage:

- MySQL
- PostgreSQL
- MongoDB
- Redis (optional persistence)
- WordPress uploads
- Application logs

---

# 3. Docker Storage Types

Docker provides three primary storage options.

| Type | Managed by Docker | Persists Data | Typical Use Case |
|------|-------------------|---------------|------------------|
| Volume | ✅ | ✅ | Databases, production workloads |
| Bind Mount | ❌ | ✅ | Development, local files |
| tmpfs | ❌ | ❌ | Temporary in-memory data |

---

# 4. Docker Volumes

A Docker Volume is a Docker-managed storage location outside the container's writable layer.

```text
Host Machine
│
├── Docker Engine
│      │
│      ▼
│   Docker Volume
│      ▲
│      │
│  Container
```

Volumes continue to exist even if the container is removed.

---

## Create a Volume

```bash
docker volume create my-volume
```

List volumes:

```bash
docker volume ls
```

Inspect a volume:

```bash
docker volume inspect my-volume
```

---

## Use a Volume

```bash
docker run -d \
--name mysql-db \
-v my-volume:/var/lib/mysql \
mysql
```

The database files are stored inside the volume instead of the container.

---

# 5. Bind Mounts

A Bind Mount connects a directory on the host machine to a directory inside the container.

```text
Host Directory
/home/user/project
        │
        ▼
Container
/app
```

Run with a bind mount:

```bash
docker run \
-v $(pwd):/app \
node
```

Any changes in the host directory are immediately visible inside the container.

---

## Bind Mount Use Cases

- Local development
- Source code editing
- Configuration files
- Static website content

---

# 6. tmpfs Mounts

A tmpfs mount stores data entirely in RAM.

Characteristics:

- Very fast
- Not written to disk
- Deleted when the container stops

Example:

```bash
docker run \
--tmpfs /cache \
nginx
```

Common use cases:

- Temporary cache
- Session data
- Sensitive temporary files

---

# 7. Volume Lifecycle

```text
Create Volume
      │
      ▼
Attach to Container
      │
      ▼
Write Data
      │
      ▼
Stop Container
      │
      ▼
Remove Container
      │
      ▼
Volume Still Exists
      │
      ▼
Attach to New Container
```

---

# 8. Managing Volumes

List volumes:

```bash
docker volume ls
```

Inspect:

```bash
docker volume inspect my-volume
```

Remove one volume:

```bash
docker volume rm my-volume
```

Remove unused volumes:

```bash
docker volume prune
```

---

# 9. Sharing Volumes Between Containers

Multiple containers can share the same volume.

```text
               Shared Volume
             /shared-data
             ▲          ▲
             │          │
      Container A   Container B
```

Example:

```bash
docker run -d \
-v shared-data:/data \
--name app1 \
ubuntu
```

```bash
docker run -d \
-v shared-data:/data \
--name app2 \
ubuntu
```

Both containers access the same data.

---

# 10. Backup & Restore Volumes

## Backup

```bash
docker run --rm \
-v my-volume:/volume \
-v $(pwd):/backup \
ubuntu \
tar czf /backup/backup.tar.gz /volume
```

---

## Restore

```bash
docker run --rm \
-v my-volume:/volume \
-v $(pwd):/backup \
ubuntu \
tar xzf /backup/backup.tar.gz -C /
```

---

# 11. Volume Drivers

Docker supports different storage drivers.

Examples:

- local (default)
- NFS
- CIFS
- Amazon EFS
- Azure Files
- Third-party plugins

List installed drivers:

```bash
docker info
```

---

# 12. Best Practices

- Use volumes for databases
- Use bind mounts during development
- Avoid storing critical data in container writable layers
- Remove unused volumes periodically
- Backup important volumes
- Use named volumes instead of anonymous volumes in production
- Apply the principle of least privilege to mounted directories

---

# Volume vs Bind Mount

| Feature | Volume | Bind Mount |
|---------|---------|------------|
| Managed by Docker | ✅ | ❌ |
| Easy Backup | ✅ | Depends |
| Portability | High | Lower |
| Production Ready | ✅ | Limited |
| Development Friendly | Good | Excellent |

---

# Hands-on Labs

## Lab 1

Create a Docker Volume.

---

## Lab 2

Mount a volume into an Nginx container.

---

## Lab 3

Write data inside the mounted volume.

---

## Lab 4

Remove the container.

---

## Lab 5

Create a new container using the same volume and verify that the data still exists.

---

## Lab 6

Create a bind mount for a local project directory.

---

## Lab 7

Backup and restore a Docker Volume.

---

# Interview Questions

## Beginner

- What is a Docker Volume?
- Why do containers lose data?
- Difference between a Volume and a Bind Mount?

---

## Intermediate

- Explain the Docker storage types.
- What is tmpfs?
- How do you share data between containers?

---

## Advanced

- Why are named volumes preferred in production?
- Explain the volume lifecycle.
- How would you back up a Docker Volume?
- Which storage type would you choose for a production database, and why?

---

# Chapter Summary

In this chapter, you learned:

- Why containers lose data
- Persistent storage concepts
- Docker Volumes
- Bind Mounts
- tmpfs Mounts
- Managing volumes
- Sharing data between containers
- Backup & Restore
- Volume Drivers
- Storage best practices
