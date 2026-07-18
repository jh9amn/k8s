# 🐳 Chapter 9 - Docker Compose

> **"Docker Compose allows you to define, configure, and run multi-container applications using a single YAML file."**

---

# 📖 Table of Contents

1. What is Docker Compose?
2. Why Docker Compose?
3. Docker Compose Architecture
4. compose.yaml File
5. Docker Compose Workflow
6. Services
7. Networks
8. Volumes
9. Environment Variables
10. Dependencies
11. Scaling Services
12. Docker Compose Commands
13. Production Best Practices
14. Hands-on Labs
15. Interview Questions
16. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Docker Compose
- Create multi-container applications
- Configure services
- Manage networks and volumes
- Use environment variables
- Scale services
- Follow production-ready Compose practices

---

# 1. What is Docker Compose?

Docker Compose is a tool for defining and managing **multi-container Docker applications** using a YAML configuration file.

Instead of running many `docker run` commands, you define everything once.

Example:

```text
compose.yaml
        │
docker compose up
        │
        ▼
+-----------------------+
| Frontend Container    |
+-----------------------+
           │
           ▼
+-----------------------+
| Backend Container     |
+-----------------------+
           │
           ▼
+-----------------------+
| Database Container    |
+-----------------------+
```

---

# 2. Why Docker Compose?

Imagine a MERN application.

Without Compose:

```bash
docker run mongodb

docker run backend

docker run frontend
```

You also need to:

- Configure networking
- Create volumes
- Set environment variables
- Maintain startup order

With Compose:

```bash
docker compose up
```

Everything starts automatically.

---

# 3. Docker Compose Architecture

```text
compose.yaml
       │
       ▼
Docker Compose CLI
       │
       ▼
Docker Engine
       │
       ▼
+------------------------+
| Services               |
| Networks               |
| Volumes                |
+------------------------+
```

---

# 4. compose.yaml Structure

Example:

```yaml
services:

  web:
    image: nginx

  db:
    image: mysql
```

A Compose file typically contains:

- Services
- Networks
- Volumes
- Environment variables
- Secrets (advanced)
- Configurations (advanced)

---

# 5. Docker Compose Workflow

```text
Write compose.yaml
        │
        ▼
docker compose up
        │
        ▼
Create Network
        │
        ▼
Create Volumes
        │
        ▼
Start Containers
```

---

# 6. Services

A service defines one container.

Example:

```yaml
services:

  web:
    image: nginx
```

Multiple services:

```yaml
services:

  frontend:
    image: nginx

  backend:
    image: node

  database:
    image: mysql
```

Each service receives:

- Container
- Network access
- DNS name
- Configuration

---

# 7. Networks

Compose automatically creates a network.

Example:

```yaml
services:

  frontend:

  backend:

networks:

  app-network:
```

Assign services:

```yaml
services:

  frontend:
    networks:
      - app-network

  backend:
    networks:
      - app-network

networks:

  app-network:
```

Now:

```text
frontend

↓

backend
```

can communicate using service names.

---

# 8. Volumes

Persist database data.

```yaml
services:

  mysql:
    image: mysql

    volumes:
      - mysql-data:/var/lib/mysql

volumes:

  mysql-data:
```

Even if the container is recreated:

Database data remains.

---

# 9. Environment Variables

Example:

```yaml
services:

  app:

    environment:

      APP_ENV: production

      PORT: 8080
```

Using an `.env` file:

```text
DB_USER=root

DB_PASSWORD=password
```

Compose:

```yaml
env_file:
  - .env
```

---

# 10. Dependencies

Use `depends_on`:

```yaml
services:

  backend:

    depends_on:

      - database
```

This ensures Docker starts the database container before the backend.

> **Note:** `depends_on` controls startup order but does **not** guarantee that the database is fully ready to accept connections. Health checks are often used for that.

---

# 11. Scaling Services

Scale a service:

```bash
docker compose up --scale web=3
```

Result:

```text
web_1

web_2

web_3
```

Useful for:

- Load balancing
- Performance testing
- Horizontal scaling

---

# 12. Docker Compose Commands

Start services:

```bash
docker compose up
```

Start in background:

```bash
docker compose up -d
```

Stop services:

```bash
docker compose down
```

View running services:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs
```

Follow logs:

```bash
docker compose logs -f
```

Restart:

```bash
docker compose restart
```

Build images:

```bash
docker compose build
```

Pull images:

```bash
docker compose pull
```

Execute a command:

```bash
docker compose exec web bash
```

---

# Complete Example

```yaml
version: "3.9"

services:

  frontend:
    image: nginx
    ports:
      - "8080:80"

  backend:
    image: node:22
    environment:
      APP_ENV: production

  database:
    image: mysql:8

    environment:
      MYSQL_ROOT_PASSWORD: password

    volumes:
      - mysql-data:/var/lib/mysql

volumes:

  mysql-data:
```

---

# 13. Production Best Practices

- Use specific image tags instead of `latest`.
- Store secrets outside the Compose file.
- Use named volumes for persistent data.
- Organize services into custom networks.
- Use health checks for dependent services.
- Keep the Compose file under version control.
- Separate development and production Compose files when needed.

---

# Hands-on Labs

## Lab 1

Create a Compose file with:

- Nginx
- MySQL

---

## Lab 2

Run the application:

```bash
docker compose up
```

---

## Lab 3

Add a named volume.

---

## Lab 4

Create a custom network.

---

## Lab 5

Scale the web service to three instances.

---

## Lab 6

Use an `.env` file for configuration.

---

# Interview Questions

## Beginner

- What is Docker Compose?
- Why use Compose instead of multiple `docker run` commands?
- What is a service in Compose?

---

## Intermediate

- Explain `depends_on`.
- How are networks created in Compose?
- How do services communicate?

---

## Advanced

- How would you deploy a three-tier application with Compose?
- Why are named volumes important?
- How do you manage environment-specific configurations?

---

# Chapter Summary

In this chapter, you learned:

- Docker Compose fundamentals
- compose.yaml structure
- Services
- Networks
- Volumes
- Environment variables
- Dependencies
- Scaling
- Common Compose commands
- Production best practices
