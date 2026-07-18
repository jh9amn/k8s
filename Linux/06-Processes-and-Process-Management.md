# ⚙️ Chapter 6 - Processes and Process Management

> **"Everything running in Linux is a process."**

Whether you're opening Chrome, running Docker, executing a Python script, or starting Kubernetes Pods, Linux is managing **processes** behind the scenes.

Understanding process management is essential for Linux administrators, DevOps engineers, SREs, and Cloud engineers because almost every production issue involves processes.

---

# 📖 Table of Contents

1. Introduction
2. What is a Process?
3. Program vs Process
4. Process Lifecycle
5. Process States
6. Process IDs (PID, PPID)
7. Parent & Child Processes
8. Daemon Processes
9. Process Memory Layout
10. Viewing Processes
11. Internal Working
12. DevOps Connection

---

# 🎯 Learning Objectives

After completing this chapter, you'll be able to:

- Explain what a process is and how it differs from a program.
- Understand the Linux process lifecycle.
- Interpret process states.
- Read and use PIDs and PPIDs.
- Identify parent-child relationships.
- Understand daemon processes.
- Inspect running processes.
- Understand how Linux creates and manages processes.
- Connect process management concepts to Docker and Kubernetes.

---

# 1. Introduction

Think about everything you're doing on your computer right now.

You might have:

- A web browser open
- A terminal running
- VS Code editing files
- Spotify playing music
- Docker Desktop running
- Kubernetes (Minikube) running

Although these appear as applications, Linux sees them all as **processes**.

A process is simply a **program that is currently executing**.

---

# Real-Life Analogy

Imagine a restaurant.

```
Recipe Book
        │
        ▼
Recipe Selected
        │
        ▼
Chef Starts Cooking
        │
        ▼
Meal Being Prepared
```

- The **recipe** is like a **program**.
- The **chef actively cooking** is like a **process**.

A program is passive. A process is active.

---

# 2. What is a Process?

A **process** is an instance of a program that is currently being executed by the operating system.

It contains:

- Executable code
- Memory
- CPU state
- Open files
- Environment variables
- Process ID (PID)
- Scheduling information

Without a running process, a program is just a file stored on disk.

---

# Example

Python file:

```python
print("Hello Linux")
```

Saved as:

```text
hello.py
```

At this point:

```
Disk

↓

hello.py
```

This is just a **program**.

When executed:

```bash
python hello.py
```

Linux creates:

```
Program

↓

Memory Allocation

↓

Assign PID

↓

Create Process

↓

CPU Executes Instructions

↓

Output Produced

↓

Process Terminates
```

---

# 3. Program vs Process

Many beginners confuse these two terms.

| Program | Process |
|----------|----------|
| Stored on disk | Running in memory |
| Passive | Active |
| Static | Dynamic |
| No PID | Has PID |
| Doesn't use CPU | Uses CPU |
| Doesn't consume RAM | Consumes RAM |

---

## Example

File:

```
nginx
```

Program.

Run:

```bash
sudo systemctl start nginx
```

Linux creates:

```
nginx

↓

Running Process

↓

PID = 1532
```

---

# Multiple Processes from One Program

One program can create many processes.

Example:

```
Google Chrome

├── Process 1
├── Process 2
├── Process 3
├── Process 4
└── Process 5
```

Modern browsers create separate processes for:

- Tabs
- Extensions
- GPU rendering
- Network handling

This improves stability and security.

---

# 4. Process Lifecycle

Every process follows a lifecycle.

```
Program
   │
   ▼
Created
   │
   ▼
Ready
   │
   ▼
Running
   │
   ▼
Waiting
   │
   ▼
Running Again
   │
   ▼
Terminated
```

### States Explained

- **Created** – The OS creates the process.
- **Ready** – Waiting for CPU time.
- **Running** – Currently executing.
- **Waiting (Blocked)** – Waiting for I/O or another event.
- **Terminated** – Process exits and resources are released.

---

# 5. Process States

Linux represents process states using single-letter codes.

| State | Meaning |
|--------|---------|
| `R` | Running or runnable |
| `S` | Sleeping (interruptible) |
| `D` | Uninterruptible sleep (usually waiting for disk I/O) |
| `T` | Stopped or traced |
| `Z` | Zombie |
| `X` | Dead (rarely seen) |

You can view process states using:

```bash
ps aux
```

Example:

```text
USER   PID %CPU %MEM STAT COMMAND
root     1  0.0  0.1 Ss   systemd
aman  3245  3.1  1.2 R    python
```

Here:

- `Ss` → Sleeping process that is a session leader.
- `R` → Running process.

---

# Zombie Process

A **Zombie** process has finished execution, but its parent has not yet collected its exit status.

Think of it as:

```
Process Finished

↓

Waiting for Parent

↓

Zombie
```

Zombie processes consume almost no CPU or memory but still occupy an entry in the process table.

---

# 6. Process IDs (PID & PPID)

Every process has a unique identifier.

### PID (Process ID)

Identifies the process itself.

### PPID (Parent Process ID)

Identifies the process that created it.

View them:

```bash
ps -ef
```

Example:

```text
UID      PID  PPID CMD
root       1     0 /sbin/init
aman    4123     1 python app.py
```

Here:

- PID = 4123
- Parent = 1 (`systemd`)

---

# PID 1

The first userspace process started by the Linux kernel is:

```text
systemd
```

It has:

```
PID = 1
```

It is responsible for:

- Starting services
- Managing daemons
- Reaping orphan processes
- Coordinating system startup

In older systems, `init` served this role.

---

# 7. Parent & Child Processes

Processes can create new processes.

Example:

```
Terminal
   │
   ▼
bash
   │
   ▼
python app.py
```

Here:

- `bash` is the parent.
- `python` is the child.

This hierarchy forms a **process tree**.

View it:

```bash
pstree
```

Example:

```text
systemd
 ├── sshd
 ├── docker
 ├── nginx
 └── bash
      └── python
```

---

# 8. Daemon Processes

A **daemon** is a background process that runs continuously to provide services.

Examples:

- `sshd`
- `cron`
- `systemd`
- `dockerd`
- `containerd`
- `kubelet`
- `nginx`

Characteristics:

- No user interaction.
- Usually starts during boot.
- Keeps running until stopped.

---

# 9. Process Memory Layout

When a process starts, Linux allocates memory in several sections.

```
+--------------------+
|     Stack          |
+--------------------+
|      Heap          |
+--------------------+
| Initialized Data   |
+--------------------+
| Uninitialized Data |
+--------------------+
|      Text          |
+--------------------+
```

- **Text** → Program instructions.
- **Data** → Global/static variables.
- **BSS** → Uninitialized variables.
- **Heap** → Dynamically allocated memory.
- **Stack** → Function calls and local variables.

---

# 10. Viewing Processes

The most common commands are:

### List all processes

```bash
ps -ef
```

or

```bash
ps aux
```

---

### Interactive process viewer

```bash
top
```

Shows:

- CPU usage
- Memory usage
- Running processes
- Load average

---

### Enhanced viewer (if installed)

```bash
htop
```

Features:

- Colorful interface
- Search
- Process tree
- Easy sorting

---

# 11. Internal Working

When you run:

```bash
python app.py
```

Linux performs:

```
Shell
   │
   ▼
fork()
   │
   ▼
Child Process Created
   │
   ▼
exec()
   │
   ▼
Program Loaded into Memory
   │
   ▼
Assign PID
   │
   ▼
Scheduler Places Process in Ready Queue
   │
   ▼
CPU Executes Process
```

The two key system calls are:

- `fork()` → Creates a new process.
- `exec()` → Replaces the process image with a new program.

---

# 🐳 Docker Connection

Each Docker container has its own process namespace.

The first process inside the container becomes:

```
PID 1
```

Example:

```bash
docker run ubuntu sleep 1000
```

Inside the container:

```bash
ps
```

Output:

```text
PID COMMAND
1 sleep
```

Unlike a normal Linux system, the container's `sleep` process is PID 1.

If PID 1 exits, the entire container stops.

---

# ☸️ Kubernetes Connection

A Kubernetes Pod is essentially one or more containers.

Each container runs one main process.

Example:

```
Pod
│
├── nginx (PID 1)
└── sidecar (PID 1 in its own container)
```

Kubernetes monitors these processes.

If the main process exits unexpectedly:

```
Container Exits

↓

Kubelet Detects Failure

↓

Restart Container
```

This is the foundation of Kubernetes' self-healing capability.

---

# 💡 Quick Recap

- A **program** is stored on disk.
- A **process** is a running program.
- Every process has a **PID** and **PPID**.
- Processes move through lifecycle states.
- Daemons provide background services.
- Linux creates processes using `fork()` and `exec()`.
- Docker containers and Kubernetes Pods revolve around managing processes.

---

---

# 12. Foreground vs Background Processes

When you execute a command in Linux, it normally runs in the **foreground**.

Example

```bash
python app.py
```

The terminal waits until the program finishes.

```
Terminal

↓

python app.py

↓

Running...

↓

Terminal Busy
```

You cannot execute another command until the current process exits (unless you open another terminal).

---

## Background Process

Sometimes you want a process to continue running while you use the terminal.

Append `&` to run it in the background.

```bash
python app.py &
```

Output

```text
[1] 4321
```

Where:

- `[1]` → Job number
- `4321` → Process ID (PID)

Now the terminal is free for other commands.

---

# Foreground vs Background

| Foreground | Background |
|------------|------------|
| Uses terminal | Doesn't block terminal |
| User interacts directly | Runs independently |
| One active foreground process per terminal | Multiple background jobs possible |
| Started normally | Started with `&` or moved using `bg` |

---

# 13. Job Control

Linux shells (Bash, Zsh, etc.) provide **job control**.

A **job** is simply a process managed by the current shell.

Useful commands:

```bash
jobs
```

Shows background and suspended jobs.

Example:

```text
[1]+ Running python app.py &
```

---

# Suspend a Process

Run:

```bash
python app.py
```

Press:

```
Ctrl + Z
```

Output

```text
[1]+ Stopped python app.py
```

The process is paused, not terminated.

---

# Resume in Background

```bash
bg
```

Output

```text
[1]+ python app.py &
```

The process continues running in the background.

---

# Bring Back to Foreground

```bash
fg
```

The job returns to the foreground.

---

# Kill a Job

```bash
kill %1
```

Here:

```
%1
```

means Job Number 1.

---

# 14. nohup

Normally,

closing the terminal also terminates child processes.

Example:

```
Terminal Closed

↓

python app.py

↓

Stops
```

To keep a process running after logout:

```bash
nohup python app.py &
```

Output:

```text
appending output to nohup.out
```

Now the process continues even if:

- SSH disconnects
- Terminal closes
- User logs out

---

# Production Example

Deploying a long-running script

```bash
nohup python backup.py &
```

The backup continues overnight even if your SSH session ends.

---

# 15. Process Signals

Linux communicates with processes using **signals**.

Signals tell a process to:

- Stop
- Pause
- Continue
- Reload configuration
- Terminate

Think of signals as messages from the kernel or another process.

---

# View Signals

```bash
kill -l
```

Example output:

```text
1) SIGHUP
2) SIGINT
9) SIGKILL
15) SIGTERM
18) SIGCONT
19) SIGSTOP
```

---

# Common Signals

| Signal | Number | Purpose |
|---------|---------|---------|
| SIGHUP | 1 | Reload configuration / Hangup |
| SIGINT | 2 | Interrupt (Ctrl+C) |
| SIGQUIT | 3 | Quit and generate core dump |
| SIGKILL | 9 | Forcefully terminate |
| SIGTERM | 15 | Gracefully terminate |
| SIGSTOP | 19 | Pause process |
| SIGCONT | 18 | Resume paused process |

---

# Ctrl + C

Pressing:

```
Ctrl + C
```

does **not** kill a process directly.

Instead, the terminal sends:

```
SIGINT
```

The application decides how to handle it.

---

# SIGTERM vs SIGKILL

## SIGTERM (15)

```bash
kill PID
```

or

```bash
kill -15 PID
```

Behavior:

```
Signal Sent

↓

Application Receives It

↓

Cleanup

↓

Close Files

↓

Release Memory

↓

Exit
```

This is the preferred way to stop a process.

---

## SIGKILL (9)

```bash
kill -9 PID
```

Behavior:

```
Kernel

↓

Immediately Stops Process

↓

No Cleanup
```

The process cannot ignore or handle SIGKILL.

Use it only if SIGTERM doesn't work.

---

# 16. kill

Stop a process using its PID.

Example

```bash
kill 4321
```

Equivalent to:

```bash
kill -15 4321
```

---

# Force Kill

```bash
kill -9 4321
```

---

# 17. pkill

Instead of PID,

kill by process name.

Example

```bash
pkill nginx
```

Kills all processes named:

```
nginx
```

---

# Kill by Pattern

```bash
pkill python
```

Stops all Python processes owned by the current user.

---

# 18. killall

Similar to `pkill`.

Example

```bash
killall firefox
```

Terminates all Firefox processes.

---

# kill vs pkill vs killall

| Command | Uses |
|----------|------|
| `kill` | PID |
| `pkill` | Process name or pattern |
| `killall` | Exact process name |

---

# 19. nice and renice

Linux schedules CPU time based on **priority**.

Priority is influenced by the **nice value**.

Range:

```text
-20  ...  19
```

- `-20` → Highest priority
- `19` → Lowest priority

---

# Start with Nice Value

```bash
nice -n 10 python app.py
```

The process gets lower CPU priority.

---

# Change Priority

```bash
renice 5 -p 4321
```

Changes the priority of PID `4321`.

---

# View Priority

```bash
ps -o pid,ni,comm
```

Example

```text
PID NI COMMAND
4321 10 python
```

---

# When to Use nice

Production examples:

- Large backup jobs
- Data imports
- Log processing
- Video encoding

These tasks can run with lower priority so they don't impact interactive users.

---

# 20. Monitoring Processes

## ps

Snapshot of current processes.

```bash
ps -ef
```

---

## top

Live monitoring.

```bash
top
```

Useful for:

- CPU usage
- Memory usage
- Load average
- Active processes

---

## htop

Enhanced interactive process monitor.

```bash
htop
```

Advantages:

- Search
- Colorized display
- Process tree
- Mouse support
- Easy sorting

---

# 21. The /proc Filesystem

Linux exposes process information through the virtual `/proc` filesystem.

Each running process has a directory named after its PID.

Example:

```bash
ls /proc
```

You might see:

```text
1
235
890
4321
```

These are process IDs.

---

## Inspect a Process

```bash
ls /proc/4321
```

Contains information about:

- Memory
- Environment variables
- File descriptors
- CPU usage
- Status

---

## Process Status

```bash
cat /proc/4321/status
```

Example output:

```text
Name: python
State: S (sleeping)
Pid: 4321
PPid: 1234
Uid: 1000
Gid: 1000
```

---

# Open Files

View files opened by a process:

```bash
ls -l /proc/4321/fd
```

Each file descriptor is represented as a symbolic link.

---

# Internal Working

When you execute:

```bash
kill 4321
```

Linux performs:

```
User

↓

Shell

↓

System Call

↓

Kernel

↓

Locate PID

↓

Send Signal

↓

Scheduler

↓

Process Receives Signal

↓

Cleanup (if applicable)

↓

Resources Released
```

---

# 🐳 Docker Connection

A container typically has one primary process.

View processes inside a container:

```bash
docker top mycontainer
```

Or:

```bash
docker exec -it mycontainer ps -ef
```

Stopping the main process usually stops the container.

---

# ☸️ Kubernetes Connection

Each container has its own PID namespace by default.

Useful commands:

```bash
kubectl exec -it pod-name -- ps -ef
```

View logs:

```bash
kubectl logs pod-name
```

Restart caused by process exit:

```bash
kubectl describe pod pod-name
```

If the main container process exits repeatedly, Kubernetes may place the Pod into:

```
CrashLoopBackOff
```

---

# Production Troubleshooting

## High CPU Usage

```bash
top
```

Identify the offending PID.

---

## High Memory Usage

```bash
ps aux --sort=-%mem
```

---

## Find Parent Process

```bash
ps -o pid,ppid,cmd
```

---

## View Process Tree

```bash
pstree
```

---

## Find Open Files

```bash
lsof -p PID
```

---

---

# 22. systemd and Service Management

Modern Linux distributions (Ubuntu, Debian, RHEL, CentOS, Fedora, etc.) use **systemd** as the default init system and service manager.

## What is systemd?

`systemd` is the **first userspace process** started by the Linux kernel.

It has:

```text
PID = 1
```

Responsibilities:

- Boot the system
- Start services
- Stop services
- Restart failed services
- Manage daemons
- Track logs (journal)
- Handle dependencies between services

---

## Boot Flow

```text
Power On
    │
    ▼
BIOS / UEFI
    │
    ▼
Bootloader (GRUB)
    │
    ▼
Linux Kernel
    │
    ▼
systemd (PID 1)
    │
    ├── Network
    ├── SSH
    ├── Docker
    ├── Containerd
    ├── kubelet
    └── User Services
```

---

# 23. systemctl Command

`systemctl` is the primary command to manage services.

### Check Service Status

```bash
systemctl status nginx
```

---

### Start a Service

```bash
sudo systemctl start nginx
```

---

### Stop a Service

```bash
sudo systemctl stop nginx
```

---

### Restart a Service

```bash
sudo systemctl restart nginx
```

---

### Reload Configuration

```bash
sudo systemctl reload nginx
```

Use reload when supported to avoid downtime.

---

### Enable at Boot

```bash
sudo systemctl enable nginx
```

---

### Disable at Boot

```bash
sudo systemctl disable nginx
```

---

### View All Running Services

```bash
systemctl list-units --type=service
```

---

### View Failed Services

```bash
systemctl --failed
```

---

# 24. Service Unit Files

Services are defined using **unit files**.

Location:

```text
/etc/systemd/system/
```

or

```text
/lib/systemd/system/
```

Example:

```ini
[Unit]
Description=My Python Application

[Service]
ExecStart=/usr/bin/python3 /opt/app/app.py
Restart=always
User=appuser

[Install]
WantedBy=multi-user.target
```

Reload systemd after editing:

```bash
sudo systemctl daemon-reload
```

---

# 25. Logs with journalctl

Instead of checking log files manually, systemd provides `journalctl`.

### View All Logs

```bash
journalctl
```

---

### View Logs for a Service

```bash
journalctl -u nginx
```

---

### Follow Logs (like `tail -f`)

```bash
journalctl -u nginx -f
```

---

### View Today's Logs

```bash
journalctl --since today
```

---

### View Logs Since 1 Hour Ago

```bash
journalctl --since "1 hour ago"
```

---

# 26. Production Troubleshooting

## Service Won't Start

Check status:

```bash
systemctl status myservice
```

Then inspect logs:

```bash
journalctl -u myservice
```

---

## High CPU Usage

Find the process:

```bash
top
```

or

```bash
htop
```

---

## High Memory Usage

```bash
ps aux --sort=-%mem | head
```

---

## Process Won't Stop

Graceful:

```bash
kill PID
```

Force:

```bash
kill -9 PID
```

---

## Port Already in Use

Find which process owns the port:

```bash
lsof -i :8080
```

or

```bash
ss -tulpn | grep 8080
```

---

## Zombie Processes

Find zombies:

```bash
ps aux | grep Z
```

Usually fix by restarting or terminating the parent process.

---

# 27. Docker Process Model

Containers are **processes isolated by namespaces and cgroups**.

Example:

```bash
docker run nginx
```

Inside the container:

```bash
ps -ef
```

Output:

```text
PID CMD
1 nginx
```

The `nginx` process is **PID 1** inside the container.

If PID 1 exits:

```text
Container Stops
```

---

## View Running Processes

```bash
docker top container_name
```

---

## Execute Commands

```bash
docker exec -it container_name bash
```

---

# 28. Kubernetes Process Model

A Pod runs one or more containers.

Each container has its own process namespace (unless shared).

Example:

```text
Pod
├── nginx container
│      └── PID 1 -> nginx
│
└── sidecar container
       └── PID 1 -> fluent-bit
```

The **kubelet** monitors these processes.

If the main process exits:

```text
Process Exits
      │
      ▼
Container Stops
      │
      ▼
Kubelet Detects Failure
      │
      ▼
Restart Container
```

---

## Useful Kubernetes Commands

Check Pods:

```bash
kubectl get pods
```

---

Describe Pod:

```bash
kubectl describe pod pod-name
```

---

View Logs:

```bash
kubectl logs pod-name
```

---

Open a Shell:

```bash
kubectl exec -it pod-name -- /bin/bash
```

---

List Processes Inside a Pod:

```bash
kubectl exec -it pod-name -- ps -ef
```

---

# 29. Hands-on Lab

## Start a Background Process

```bash
sleep 300 &
```

View jobs:

```bash
jobs
```

Bring it back:

```bash
fg
```

---

## Suspend a Process

```bash
sleep 500
```

Press:

```text
Ctrl + Z
```

Resume:

```bash
bg
```

---

## Start a Process with nohup

```bash
nohup sleep 600 &
```

---

## Find Its PID

```bash
ps -ef | grep sleep
```

---

## Terminate It

```bash
kill PID
```

---

## Monitor Processes

```bash
top
```

---

## Inspect Process Details

```bash
cat /proc/PID/status
```

Replace `PID` with the actual process ID.

---

# 30. Interview Questions

## Beginner

1. What is a process?
2. Difference between a program and a process?
3. What is a PID?
4. What is a PPID?
5. What is a daemon?

---

## Intermediate

1. Explain the Linux process lifecycle.
2. What is a zombie process?
3. Difference between foreground and background processes?
4. Explain `fork()` and `exec()`.
5. Difference between `kill`, `pkill`, and `killall`?

---

## Advanced

1. Why should `SIGTERM` be preferred over `SIGKILL`?
2. What happens when PID 1 exits in a Docker container?
3. Explain how Kubernetes restarts failed containers.
4. How does `systemd` manage services?
5. What information is available in `/proc`?
6. Explain process namespaces and cgroups.
7. How would you troubleshoot a `CrashLoopBackOff` caused by a process repeatedly exiting?

---

# 31. One-Page Cheat Sheet

| Command | Purpose |
|---------|---------|
| `ps -ef` | List all processes |
| `ps aux` | Detailed process list |
| `top` | Live process monitor |
| `htop` | Interactive process viewer |
| `jobs` | Show shell jobs |
| `bg` | Resume job in background |
| `fg` | Bring job to foreground |
| `nohup cmd &` | Run after logout |
| `kill PID` | Gracefully terminate |
| `kill -9 PID` | Force kill |
| `pkill name` | Kill by process name |
| `killall name` | Kill all matching processes |
| `nice -n 10 cmd` | Start with lower priority |
| `renice 5 -p PID` | Change priority |
| `pstree` | Show process tree |
| `lsof -p PID` | Open files of a process |
| `systemctl status svc` | Service status |
| `systemctl restart svc` | Restart service |
| `journalctl -u svc` | View service logs |
| `journalctl -u svc -f` | Follow service logs |
| `kubectl logs pod` | Pod logs |
| `kubectl exec -it pod -- ps -ef` | Processes inside a Pod |

---

# 32. Common Mistakes

❌ Using `kill -9` as the first option instead of trying `SIGTERM`.

❌ Running long jobs without `nohup` over SSH.

❌ Ignoring logs in `journalctl` when troubleshooting services.

❌ Forgetting that the main process (PID 1) controls a Docker container's lifecycle.

❌ Assuming `top` shows historical data—it only shows current activity.

---

# 33. Best Practices

- Stop applications gracefully with `SIGTERM` before using `SIGKILL`.
- Monitor CPU and memory regularly using `top` or `htop`.
- Use `journalctl` instead of searching multiple log files when systemd is available.
- Run services as dedicated non-root users whenever possible.
- Understand the parent-child relationship when debugging orphan or zombie processes.
- In containers, ensure the main application handles signals correctly so it shuts down cleanly.
- In Kubernetes, investigate logs (`kubectl logs`) and events (`kubectl describe pod`) before restarting Pods.

---

# 34. Chapter Summary

In this chapter, you learned:

- What a process is and how it differs from a program.
- The Linux process lifecycle and process states.
- PIDs, PPIDs, parent-child relationships, and daemons.
- How Linux creates processes with `fork()` and `exec()`.
- Foreground and background jobs.
- Job control (`jobs`, `bg`, `fg`).
- Long-running processes with `nohup`.
- Signals (`SIGTERM`, `SIGKILL`, `SIGINT`, etc.).
- Process management commands (`kill`, `pkill`, `killall`).
- Process priorities (`nice`, `renice`).
- Monitoring tools (`ps`, `top`, `htop`).
- The `/proc` filesystem.
- Managing services with `systemd` and `systemctl`.
- Viewing logs with `journalctl`.
- Docker and Kubernetes process models.
- Production troubleshooting techniques.
