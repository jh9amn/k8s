# 📚 Chapter 1 - Introduction to Linux & Linux Architecture

> **Linux is the foundation of modern DevOps, Cloud Computing, Containers, and Kubernetes.**
>
> Nearly every cloud server, Docker container, Kubernetes node, CI/CD server, and production infrastructure runs Linux.

---

# 📖 Table of Contents

1. What is Linux?
2. History of Linux
3. Why Linux?
4. Linux vs Unix
5. Linux Distributions
6. Linux Architecture
7. Kernel
8. Shell
9. File System
10. User Space vs Kernel Space
11. Linux Boot Process
12. Why DevOps Engineers Love Linux
13. Linux in Real World
14. Common Myths
15. Important Commands
16. Best Practices
17. Interview Questions
18. Quick Revision

---

# What is Linux?

Linux is an **open-source operating system kernel** created by **Linus Torvalds** in **1991**.

An Operating System (OS) is software that acts as a bridge between the **user** and the **computer hardware**.

```
User

↓

Applications

↓

Operating System

↓

Hardware
```

Without an operating system:

- Programs cannot run.
- Hardware cannot be managed.
- Files cannot be stored.
- Memory cannot be allocated.

Linux is the operating system behind:

- Cloud Servers
- Android Phones
- Docker Containers
- Kubernetes Nodes
- Supercomputers
- IoT Devices
- Raspberry Pi
- Most Web Servers

---

# What is an Operating System?

An Operating System manages:

- CPU
- Memory (RAM)
- Disk Storage
- Keyboard
- Mouse
- Network
- Processes
- Files

Example:

```
You open Google Chrome.

↓

Chrome requests memory.

↓

Operating System allocates RAM.

↓

CPU executes instructions.

↓

Screen displays output.
```

The OS makes hardware usable for applications.

---

# History of Linux

### 1969

UNIX created at Bell Labs.

↓

### 1983

GNU Project started by Richard Stallman.

↓

### 1991

Linus Torvalds created the Linux Kernel.

↓

### 1992

GNU tools + Linux Kernel

↓

GNU/Linux Operating System

↓

Today

Millions of Linux Servers worldwide.

---

# Why Linux?

Linux became popular because it is:

✅ Free

✅ Open Source

✅ Stable

✅ Secure

✅ Fast

✅ Lightweight

✅ Highly Customizable

✅ Excellent for Servers

Because of these features, almost every cloud provider supports Linux as the primary operating system.

---

# Linux vs Unix

| Linux | Unix |
|--------|------|
| Open Source | Mostly Proprietary |
| Free | Commercial in many cases |
| Community Driven | Vendor Driven |
| Highly Customizable | Less Flexible |
| Most Popular for Cloud | Common in Enterprise Legacy Systems |

Linux was inspired by UNIX but is not the same operating system.

---

# What is Open Source?

Open source means the source code is publicly available.

Anyone can:

- View it
- Modify it
- Improve it
- Share it (subject to license terms)

Benefits:

```
Thousands of Developers

↓

Find Bugs

↓

Improve Linux

↓

Better Security

↓

Better Performance
```

---

# Linux Distributions (Distros)

The Linux **kernel** is the core of the operating system.

A Linux distribution packages the kernel together with utilities, package managers, libraries, and desktop environments.

Popular distributions:

| Distribution | Common Usage |
|--------------|--------------|
| Ubuntu | Beginners, Servers |
| Debian | Stable Servers |
| CentOS Stream | Enterprise |
| Red Hat Enterprise Linux (RHEL) | Enterprise Production |
| Rocky Linux | RHEL-compatible |
| AlmaLinux | RHEL-compatible |
| Fedora | Latest Features |
| Arch Linux | Advanced Users |
| Kali Linux | Security Testing |

---

# Linux Architecture

```
                User

                  │

                  ▼

            Applications

                  │

                  ▼

               Shell

                  │

                  ▼

               Kernel

                  │

                  ▼

             Hardware
```

Each layer has a specific responsibility.

---

# User

A user interacts with the system.

Examples:

- Opening applications
- Running commands
- Creating files
- Managing processes

---

# Applications

Applications include:

- Chrome
- VS Code
- Docker
- Nginx
- MySQL
- Python
- Java

Applications request services from the operating system.

---

# Shell

The Shell is a command interpreter.

Example:

```bash
ls
```

The shell interprets the command and asks the kernel to execute it.

Popular shells:

- Bash
- Zsh
- Fish
- Sh
- Ksh

---

# Kernel

The **Kernel** is the heart of Linux.

It directly communicates with hardware.

Responsibilities:

- Process Management
- Memory Management
- Device Drivers
- File System
- CPU Scheduling
- Networking
- Security

Without the kernel:

```
Application

↓

Cannot Access Hardware
```

---

# Kernel Responsibilities

## Process Management

Starts and stops programs.

Example:

```
Google Chrome

↓

Kernel Creates Process
```

---

## Memory Management

Allocates RAM to applications.

```
Python

↓

Requests Memory

↓

Kernel Allocates RAM
```

---

## Device Management

Handles:

- Keyboard
- Mouse
- Printer
- USB
- GPU
- Disk

using **device drivers**.

---

## File System Management

The kernel manages:

- Read files
- Write files
- Delete files
- Create directories
- Permissions

---

## CPU Scheduling

Many programs run simultaneously.

The kernel decides:

```
Which process

↓

Gets CPU

↓

For how long
```

This enables multitasking.

---

## Networking

The kernel manages:

- TCP/IP
- Network interfaces
- Routing
- Sockets
- Firewalls (with supporting tools)

---

# User Space vs Kernel Space

Linux separates execution into two areas.

```
Application

↓

User Space

↓

System Call

↓

Kernel Space

↓

Hardware
```

## User Space

Applications run here.

They cannot directly access hardware.

---

## Kernel Space

The kernel runs here.

It has full access to:

- CPU
- Memory
- Disk
- Network
- Devices

This separation improves stability and security.

---

# What is a System Call?

Applications cannot directly access hardware.

Instead they request services from the kernel.

Example:

```c
open("file.txt")
```

Flow:

```
Application

↓

System Call

↓

Kernel

↓

Disk

↓

Data Returned
```

Common system calls:

- open()
- read()
- write()
- close()
- fork()
- execve()

---

# Linux Boot Process (Overview)

```
Power On

↓

BIOS / UEFI

↓

Bootloader (GRUB)

↓

Linux Kernel

↓

Init System (systemd)

↓

Services Start

↓

Login Prompt
```

We'll explore each step in a later chapter.

---

# Why DevOps Engineers Love Linux

Almost every DevOps tool runs best on Linux.

Examples:

| Tool | Runs on Linux |
|------|---------------|
| Docker | ✅ |
| Kubernetes | ✅ |
| Jenkins | ✅ |
| Git | ✅ |
| Terraform | ✅ |
| Ansible | ✅ |
| Nginx | ✅ |
| Apache | ✅ |
| Prometheus | ✅ |
| Grafana | ✅ |

---

# Linux in the Real World

Linux powers:

- AWS EC2 instances
- Azure Virtual Machines
- Google Compute Engine
- Docker containers
- Kubernetes worker nodes
- Web servers
- Database servers
- CI/CD servers
- NAS devices
- Supercomputers

Fun fact:

Over **90% of the world's top supercomputers** run Linux.

---

# Common Myths

❌ Linux is only for hackers.

✔ Linux is widely used by developers, enterprises, banks, governments, and cloud providers.

---

❌ Linux has no GUI.

✔ Many distributions include desktop environments like GNOME and KDE.

---

❌ Linux is difficult.

✔ Modern distributions such as Ubuntu are beginner-friendly.

---

# Useful Commands

Check current user:

```bash
whoami
```

Check kernel version:

```bash
uname -r
```

Check OS information:

```bash
cat /etc/os-release
```

Show system architecture:

```bash
uname -m
```

Show all kernel information:

```bash
uname -a
```

Display hostname:

```bash
hostname
```

Display current date and time:

```bash
date
```

Display system uptime:

```bash
uptime
```

---

# Best Practices

✅ Learn the command line before relying on a GUI.

✅ Understand the Linux file system hierarchy.

✅ Learn Bash basics.

✅ Practice daily using the terminal.

✅ Read command manuals with:

```bash
man <command>
```

---

# Interview Questions

## Beginner

1. What is Linux?
2. What is an operating system?
3. What is the Linux kernel?
4. Difference between Linux and Unix?
5. What is a Linux distribution?

---

## Intermediate

6. Explain Linux architecture.
7. What is a system call?
8. Difference between User Space and Kernel Space?
9. What are the responsibilities of the kernel?
10. Explain the Linux boot process.

---

## Advanced

11. How does an application communicate with hardware?
12. Why does Linux separate User Space and Kernel Space?
13. Explain process scheduling.
14. Why is Linux preferred in cloud computing?
15. How does the kernel manage memory?

---

# 📝 Quick Revision

- Linux is an open-source operating system kernel.
- The kernel manages hardware resources.
- Applications communicate with hardware through system calls.
- The shell interprets user commands.
- Linux separates User Space and Kernel Space for security and stability.
- Linux distributions package the kernel with utilities and software.
- Linux powers most servers, cloud platforms, containers, and Kubernetes clusters.
