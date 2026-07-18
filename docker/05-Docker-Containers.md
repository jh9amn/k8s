# 🐳 Chapter 5 - Docker Containers

> **"A Docker Container is a lightweight, isolated process created from a Docker Image. Containers are the fundamental unit of execution in Docker."**

---

# 📖 Table of Contents

1. What is a Docker Container?
2. Container vs Image
3. Container Lifecycle
4. Creating Containers
5. Running Containers
6. Interactive Mode
7. Detached Mode
8. Container States
9. Listing Containers
10. Starting & Stopping Containers
11. Restarting Containers
12. Pausing & Unpausing Containers
13. Renaming Containers
14. Inspecting Containers
15. Executing Commands Inside Containers
16. Container Logs
17. Resource Limits
18. Restart Policies
19. Health Checks
20. Removing Containers
21. Hands-on Labs
22. Interview Questions
23. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Docker containers
- Create and manage containers
- Understand the complete container lifecycle
- Work in interactive and detached modes
- Inspect running containers
- View logs
- Execute commands inside containers
- Configure restart policies
- Set CPU and memory limits

---

# 1. What is a Docker Container?

A **Docker Container** is a running instance of a Docker Image.

An image is a template.

A container is the actual running application.

Example:

```text
Ubuntu Image

      │

 ┌────┴─────┐

 ▼          ▼

Container1  Container2
```

Each container has:

- Process ID (PID)
- Filesystem
- Network interface
- Hostname
- Writable layer
- Isolated process space

---

# 2. Image vs Container

| Image | Container |
|--------|-----------|
| Blueprint | Running instance |
| Read-only | Writable |
| Static | Dynamic |
| Stored on disk | Running in memory |
| Can create many containers | Executes application |

---

# 3. Container Lifecycle

```text
docker create
        │
        ▼
Created
        │
docker start
        ▼
Running
        │
docker pause
        ▼
Paused
        │
docker unpause
        ▼
Running
        │
docker stop
        ▼
Exited
        │
docker rm
        ▼
Removed
```

---

# 4. Creating Containers

Create without starting:

```bash
docker create nginx
```

Docker returns the container ID.

The container is created but not running.

---

# 5. Running Containers

Run a container:

```bash
docker run nginx
```

Internally Docker performs:

1. Pull image (if missing)
2. Create writable layer
3. Create container
4. Configure networking
5. Start the main process

---

Run with a name:

```bash
docker run --name web nginx
```

---

Run a specific image version:

```bash
docker run ubuntu:24.04
```

---

# 6. Interactive Mode

Interactive mode allows you to work inside the container.

```bash
docker run -it ubuntu
```

Explanation:

```text
-i → Interactive

-t → Allocate terminal
```

Now you're inside the container.

Example:

```bash
root@container:/#
```

Exit:

```bash
exit
```

---

# 7. Detached Mode

Run in the background:

```bash
docker run -d nginx
```

Docker returns:

```text
Container ID
```

The terminal remains available.

---

# 8. Container States

```text
Created

↓

Running

↓

Paused

↓

Exited

↓

Removed
```

View running containers:

```bash
docker ps
```

View all containers:

```bash
docker ps -a
```

---

# 9. Listing Containers

Running containers:

```bash
docker ps
```

All containers:

```bash
docker ps -a
```

Only container IDs:

```bash
docker ps -q
```

Latest container:

```bash
docker ps -l
```

---

# 10. Starting & Stopping Containers

Start:

```bash
docker start web
```

Stop:

```bash
docker stop web
```

Force stop:

```bash
docker kill web
```

Difference:

```text
docker stop
↓

Graceful shutdown (SIGTERM → SIGKILL)

docker kill
↓

Immediate SIGKILL
```

---

# 11. Restarting Containers

Restart:

```bash
docker restart web
```

---

# 12. Pausing Containers

Pause:

```bash
docker pause web
```

Resume:

```bash
docker unpause web
```

Pause freezes running processes without stopping the container.

---

# 13. Renaming Containers

Rename:

```bash
docker rename web nginx-server
```

---

# 14. Inspecting Containers

Inspect metadata:

```bash
docker inspect web
```

Useful information:

- IP address
- Mounts
- Networks
- Environment variables
- Restart policy
- Labels
- Container ID

---

# 15. Executing Commands Inside Containers

Execute a shell:

```bash
docker exec -it web bash
```

If Bash isn't installed:

```bash
docker exec -it web sh
```

Run a single command:

```bash
docker exec web ls /
```

---

# 16. Container Logs

View logs:

```bash
docker logs web
```

Follow logs:

```bash
docker logs -f web
```

Last 20 lines:

```bash
docker logs --tail 20 web
```

Include timestamps:

```bash
docker logs -t web
```

---

# 17. Resource Limits

Limit memory:

```bash
docker run -m 512m nginx
```

Limit CPU:

```bash
docker run --cpus="2" nginx
```

Both:

```bash
docker run \
-m 1g \
--cpus="2" \
nginx
```

Benefits:

- Prevent resource exhaustion
- Fair resource allocation
- Improve stability

---

# 18. Restart Policies

Always restart:

```bash
docker run \
--restart always \
nginx
```

Restart unless manually stopped:

```bash
docker run \
--restart unless-stopped \
nginx
```

Restart on failure:

```bash
docker run \
--restart on-failure \
nginx
```

Policies:

| Policy | Description |
|----------|-------------|
| no | Never restart |
| always | Always restart |
| on-failure | Restart only on errors |
| unless-stopped | Restart unless manually stopped |

---

# 19. Health Checks

Health checks determine whether an application inside the container is functioning correctly.

Example:

```bash
docker run \
--health-cmd="curl -f http://localhost || exit 1" \
nginx
```

View health status:

```bash
docker inspect web
```

Look for:

```text
Health
```

---

# 20. Removing Containers

Remove stopped container:

```bash
docker rm web
```

Force remove:

```bash
docker rm -f web
```

Remove all stopped containers:

```bash
docker container prune
```

---

# Hands-on Labs

## Lab 1

Run an Ubuntu container in interactive mode.

---

## Lab 2

Run an Nginx container in detached mode.

---

## Lab 3

View running containers.

---

## Lab 4

Inspect a running container.

---

## Lab 5

View container logs.

---

## Lab 6

Execute commands inside a running container.

---

## Lab 7

Configure a restart policy.

---

## Lab 8

Run a container with:

- 1 CPU
- 512 MB RAM

---

# Interview Questions

## Beginner

- What is a Docker Container?
- Difference between Image and Container?
- What does `docker run` do?
- Difference between `docker create` and `docker run`?

---

## Intermediate

- Explain the container lifecycle.
- Difference between `docker stop` and `docker kill`.
- Difference between interactive and detached mode.
- What is `docker exec`?

---

## Advanced

- What happens internally when a container starts?
- Explain restart policies.
- How are health checks useful?
- How do resource limits work?

---

# Chapter Summary

In this chapter, you learned:

- Docker Containers
- Container Lifecycle
- Interactive Mode
- Detached Mode
- Starting & Stopping Containers
- Logs
- Exec
- Inspect
- Restart Policies
- Health Checks
- Resource Limits
- Container Cleanup
