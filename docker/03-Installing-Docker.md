# 🐳 Chapter 3 - Installing Docker

> **"Docker installation is the first practical step toward containerization. A correct installation ensures a stable development and production environment."**

---

# 📖 Table of Contents

1. Prerequisites
2. Installing Docker on Ubuntu
3. Installing Docker on Debian
4. Installing Docker on RHEL/CentOS/Rocky Linux
5. Installing Docker Desktop on Windows
6. Installing Docker Desktop on macOS
7. Verifying the Installation
8. Docker Service Management
9. Configure Docker Without sudo
10. Docker Version Information
11. First Docker Commands
12. Uninstall Docker
13. Common Installation Issues
14. Hands-on Labs
15. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Install Docker on Linux
- Install Docker Desktop on Windows
- Verify Docker installation
- Manage the Docker service
- Configure Docker for non-root users
- Run your first container

---

# 1. Prerequisites

Before installing Docker, ensure your system meets these requirements:

## Ubuntu

- Ubuntu 22.04 or later
- 64-bit architecture
- Internet connection
- User with sudo privileges

---

## Windows

- Windows 10/11 (64-bit)
- WSL2 enabled
- Virtualization enabled in BIOS
- Docker Desktop

---

## macOS

- Apple Silicon (M1/M2/M3) or Intel
- Docker Desktop

---

# 2. Installing Docker on Ubuntu

## Step 1: Update the Package Index

```bash
sudo apt update
```

---

## Step 2: Install Required Packages

```bash
sudo apt install \
ca-certificates \
curl \
gnupg \
lsb-release
```

---

## Step 3: Add Docker's Official GPG Key

```bash
sudo mkdir -p /etc/apt/keyrings
```

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

---

## Step 4: Add Docker Repository

```bash
echo \
"deb [arch=$(dpkg --print-architecture) \
signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

---

## Step 5: Update Package List Again

```bash
sudo apt update
```

---

## Step 6: Install Docker

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

---

# 3. Installing Docker on Debian

The installation steps are similar to Ubuntu:

```bash
sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io
```

---

# 4. Installing Docker on RHEL / CentOS / Rocky Linux

Install required tools:

```bash
sudo dnf install dnf-plugins-core
```

Add Docker repository:

```bash
sudo dnf config-manager --add-repo \
https://download.docker.com/linux/centos/docker-ce.repo
```

Install Docker:

```bash
sudo dnf install docker-ce docker-ce-cli containerd.io
```

Start Docker:

```bash
sudo systemctl enable --now docker
```

---

# 5. Installing Docker Desktop on Windows

## Requirements

- Windows 10/11
- WSL2
- Virtualization enabled

Steps:

1. Download Docker Desktop.
2. Run the installer.
3. Enable the WSL2 backend.
4. Restart the system if prompted.
5. Launch Docker Desktop.
6. Wait until Docker Engine starts.

---

# 6. Installing Docker Desktop on macOS

Steps:

1. Download Docker Desktop for your processor architecture.
2. Drag Docker to the Applications folder.
3. Launch Docker Desktop.
4. Wait for Docker Engine to start.

---

# 7. Verify the Installation

Check Docker version:

```bash
docker --version
```

Example output:

```text
Docker version 28.x.x
```

---

Check detailed information:

```bash
docker info
```

---

Verify the daemon:

```bash
docker version
```

---

Run the official test image:

```bash
docker run hello-world
```

Expected output:

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

---

# 8. Docker Service Management

Check Docker status:

```bash
sudo systemctl status docker
```

Start Docker:

```bash
sudo systemctl start docker
```

Stop Docker:

```bash
sudo systemctl stop docker
```

Restart Docker:

```bash
sudo systemctl restart docker
```

Enable Docker at boot:

```bash
sudo systemctl enable docker
```

Disable automatic startup:

```bash
sudo systemctl disable docker
```

---

# 9. Configure Docker Without sudo

By default, only the root user can run Docker commands.

Add your user to the `docker` group:

```bash
sudo usermod -aG docker $USER
```

Apply the new group membership:

```bash
newgrp docker
```

Verify:

```bash
docker run hello-world
```

You should now be able to run Docker commands without `sudo`.

---

# 10. Docker Version Information

Show Docker version:

```bash
docker --version
```

Show detailed client and server versions:

```bash
docker version
```

Show system information:

```bash
docker info
```

---

# 11. First Docker Commands

List local images:

```bash
docker images
```

List running containers:

```bash
docker ps
```

List all containers:

```bash
docker ps -a
```

Pull an image:

```bash
docker pull nginx
```

Run a container:

```bash
docker run nginx
```

Run a container in the background:

```bash
docker run -d nginx
```

Stop a container:

```bash
docker stop <container_id>
```

Remove a container:

```bash
docker rm <container_id>
```

Remove an image:

```bash
docker rmi nginx
```

---

# 12. Uninstall Docker

Ubuntu:

```bash
sudo apt remove docker-ce docker-ce-cli containerd.io
```

Remove unused files:

```bash
sudo rm -rf /var/lib/docker
```

---

# 13. Common Installation Issues

## Permission Denied

Error:

```text
permission denied while trying to connect to the Docker daemon socket
```

Solution:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## Docker Service Not Running

Check:

```bash
systemctl status docker
```

Start:

```bash
sudo systemctl start docker
```

---

## Command Not Found

Verify installation:

```bash
docker --version
```

If Docker is not installed, reinstall using the official repository.

---

## Cannot Connect to Docker Daemon

Check:

```bash
sudo systemctl status docker
```

Restart:

```bash
sudo systemctl restart docker
```

---

# Hands-on Labs

## Lab 1

Install Docker on your operating system.

---

## Lab 2

Run the `hello-world` container.

---

## Lab 3

Run an Nginx container.

---

## Lab 4

Enable Docker to start automatically after system boot.

---

## Lab 5

Configure Docker so your user can run commands without `sudo`.

---

# Chapter Summary

In this chapter, you learned:

- Installing Docker on Ubuntu
- Installing Docker on Debian
- Installing Docker on RHEL-based distributions
- Installing Docker Desktop on Windows
- Installing Docker Desktop on macOS
- Verifying Docker installation
- Managing the Docker service
- Running Docker without `sudo`
- Basic Docker commands
- Common installation issues
- Building Custom Images
- Image Optimization
- Best Practices
