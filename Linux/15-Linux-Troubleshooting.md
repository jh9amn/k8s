# 🛠️ Chapter 15 - Linux Troubleshooting

> **"Troubleshooting is the art of finding the root cause of a problem—not just fixing the symptoms."**

---

# 📖 Table of Contents

1. Introduction to Linux Troubleshooting
2. Troubleshooting Methodology
3. Gathering System Information
4. Boot Process Troubleshooting
5. Bootloader (GRUB) Issues
6. Service Troubleshooting
7. System Logs
8. Essential Troubleshooting Commands
9. Hands-on Labs
10. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you'll be able to:

- Follow a structured troubleshooting methodology
- Diagnose Linux boot problems
- Troubleshoot failed services
- Read and analyze system logs
- Collect system information efficiently
- Use common troubleshooting commands
- Apply systematic debugging techniques

---

# 1. Introduction to Linux Troubleshooting

Linux troubleshooting is the process of identifying, analyzing, and resolving issues affecting a Linux system.

Problems may occur due to:

- Hardware failures
- Software bugs
- Configuration mistakes
- Network issues
- Security incidents
- Resource exhaustion

A structured troubleshooting approach minimizes downtime and prevents recurring problems.

---

# Why Troubleshooting Matters

A good Linux administrator should not only know commands but also understand **why** a problem occurs.

Effective troubleshooting helps:

- Restore services quickly
- Reduce downtime
- Prevent data loss
- Improve system reliability
- Increase customer satisfaction

---

# 2. Troubleshooting Methodology

Avoid guessing. Follow a systematic approach.

```text
Identify Problem
        │
        ▼
Collect Information
        │
        ▼
Analyze Symptoms
        │
        ▼
Find Root Cause
        │
        ▼
Apply Fix
        │
        ▼
Verify Solution
        │
        ▼
Document Findings
```

---

## Step 1: Identify the Problem

Questions to ask:

- What is not working?
- When did the issue begin?
- Is the issue reproducible?
- Who is affected?
- Were any recent changes made?

---

## Step 2: Gather Information

Collect system details before making changes.

Useful commands:

```bash
hostnamectl
```

```bash
uname -a
```

```bash
uptime
```

```bash
whoami
```

```bash
date
```

---

## Step 3: Analyze the Symptoms

Look for:

- Error messages
- Failed services
- High CPU usage
- Memory exhaustion
- Disk full
- Network failures

---

## Step 4: Identify the Root Cause

Examples:

- Incorrect configuration
- Missing package
- Permission issue
- Network outage
- Corrupted filesystem
- Resource exhaustion

Always fix the **root cause**, not just the symptoms.

---

## Step 5: Apply the Solution

Before making changes:

- Create backups if necessary.
- Test changes in a non-production environment.
- Record what you changed.

---

## Step 6: Verify

Confirm:

- Service is running.
- Logs are clean.
- Users can access the application.
- Monitoring alerts are resolved.

---

## Step 7: Document

Record:

- Problem description
- Root cause
- Commands used
- Solution
- Preventive actions

Good documentation saves time during future incidents.

---

# 3. Gathering System Information

When troubleshooting, start by collecting system information.

---

## Hostname

```bash
hostname
```

Detailed information:

```bash
hostnamectl
```

Example:

```text
Static hostname: web-server-01

Operating System: Ubuntu 24.04

Kernel: Linux 6.x
```

---

## Kernel Version

```bash
uname -r
```

Complete information:

```bash
uname -a
```

---

## Operating System

```bash
cat /etc/os-release
```

---

## Uptime

```bash
uptime
```

Example:

```text
10:25:10 up 15 days,
3 users,
load average:
0.42 0.36 0.31
```

---

## Logged-in Users

```bash
who
```

Current activity:

```bash
w
```

---

## System Date

```bash
date
```

---

# 4. Essential Troubleshooting Commands

These commands are used frequently during investigations.

---

## CPU Usage

```bash
top
```

or

```bash
htop
```

---

## Memory

```bash
free -h
```

---

## Disk Usage

```bash
df -h
```

---

## Directory Size

```bash
du -sh *
```

---

## Processes

```bash
ps aux
```

---

## Search Process

```bash
ps aux | grep nginx
```

---

## Open Files

```bash
lsof
```

---

## Kernel Messages

```bash
dmesg
```

---

## System Logs

```bash
journalctl
```

Follow logs in real time:

```bash
journalctl -f
```

---

# 5. Boot Process Overview

Understanding the Linux boot process helps diagnose startup failures.

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
initramfs
    │
    ▼
systemd
    │
    ▼
Services
    │
    ▼
Login Prompt
```

---

# Common Boot Problems

- GRUB missing
- Kernel panic
- Filesystem corruption
- Missing initramfs
- Incorrect `/etc/fstab`
- Disk failure

---

# 6. Bootloader (GRUB)

GRUB (GRand Unified Bootloader) loads the Linux kernel.

Configuration file:

```text
/etc/default/grub
```

Generate configuration:

Ubuntu:

```bash
sudo update-grub
```

RHEL:

```bash
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

---

# Recovering GRUB

Typical steps:

1. Boot from a Linux Live USB.
2. Mount the Linux root partition.
3. Chroot into the installed system.
4. Reinstall GRUB.
5. Regenerate the configuration.
6. Reboot.

> Exact commands vary by distribution and boot mode (BIOS vs UEFI).

---

# 7. Service Troubleshooting

Many production issues involve failed services.

Check service status:

```bash
systemctl status nginx
```

Start:

```bash
sudo systemctl start nginx
```

Stop:

```bash
sudo systemctl stop nginx
```

Restart:

```bash
sudo systemctl restart nginx
```

Reload configuration:

```bash
sudo systemctl reload nginx
```

Enable at boot:

```bash
sudo systemctl enable nginx
```

---

# Investigate a Failed Service

Example workflow:

```text
Application Down
        │
        ▼
systemctl status
        │
        ▼
journalctl -u service
        │
        ▼
Check Configuration
        │
        ▼
Restart Service
        │
        ▼
Verify
```

---

# Example: Nginx Not Starting

Check status:

```bash
systemctl status nginx
```

View logs:

```bash
journalctl -u nginx
```

Test configuration:

```bash
nginx -t
```

Restart:

```bash
systemctl restart nginx
```

---

# Hands-on Labs

## Lab 1

Collect system information:

```bash
hostnamectl

uname -a

uptime

date
```

---

## Lab 2

Check system logs:

```bash
journalctl -b
```

---

## Lab 3

View a service status:

```bash
systemctl status ssh
```

---

## Lab 4

Test an Nginx configuration (if installed):

```bash
sudo nginx -t
```

---

# Chapter Progress

✅ Troubleshooting Methodology

✅ System Information

✅ Boot Process

✅ GRUB

✅ Service Troubleshooting

✅ Essential Commands

---
---

# 8. CPU Troubleshooting

High CPU usage is a common issue that can make a Linux system slow or unresponsive.

Symptoms:

- Slow application response
- High load average
- Delayed user logins
- Increased request latency

---

## Check CPU Usage

View real-time CPU usage:

```bash
top
```

or

```bash
htop
```

Example:

```text
%Cpu(s): 92.4 us, 4.1 sy, 3.5 id
```

Where:

| Field | Meaning |
|--------|---------|
| us | User processes |
| sy | System (kernel) processes |
| id | Idle CPU |
| wa | I/O wait |

---

## Find CPU-Intensive Processes

```bash
ps aux --sort=-%cpu | head
```

Example:

```text
USER      PID %CPU COMMAND
root     2134 95.3 java
```

Inspect the process:

```bash
ps -fp 2134
```

---

## Load Average

Display:

```bash
uptime
```

Example:

```text
load average: 0.45 0.60 0.72
```

These values represent:

- Last 1 minute
- Last 5 minutes
- Last 15 minutes

Rule of thumb:

If a server has **4 CPU cores**:

- Load < 4 → Normal
- Load > 4 → System is overloaded

---

# Common Causes

- Infinite loops
- High traffic
- Malware
- Poorly optimized applications
- Too many background jobs

---

# 9. Memory Troubleshooting

Memory shortages may lead to:

- Slow performance
- Application crashes
- Out-of-memory (OOM) kills
- Swap usage

---

## Check Memory

```bash
free -h
```

Example:

```text
Total: 8G

Used: 6G

Free: 1G

Swap: 1G
```

---

## Monitor Processes

```bash
top
```

Sort by memory:

- Press **M** inside `top`

Or use:

```bash
ps aux --sort=-%mem | head
```

---

## Check OOM Events

```bash
dmesg | grep -i oom
```

or

```bash
journalctl | grep -i oom
```

---

# Common Causes

- Memory leaks
- Large applications
- Too many containers
- Insufficient RAM

---

# 10. Disk Troubleshooting

Symptoms:

- "No space left on device"
- Slow writes
- Applications fail to start

---

## Check Disk Usage

```bash
df -h
```

Example:

```text
Filesystem Size Used Avail Use%
/dev/sda1 100G 96G 4G 96%
```

---

## Find Large Directories

```bash
du -sh /*
```

Largest first:

```bash
du -sh /* | sort -h
```

---

## Find Large Files

```bash
find / -type f -size +1G
```

---

## Check Inode Usage

Sometimes space is available but inodes are exhausted.

```bash
df -i
```

---

# Common Causes

- Log files
- Core dumps
- Backups
- Temporary files
- Docker images
- Container logs

---

# 11. Filesystem Errors

Filesystem corruption can occur due to:

- Power failures
- Disk failures
- Improper shutdowns

Symptoms:

- Read-only filesystem
- Mount failures
- Missing files

---

## Check Filesystem

```bash
sudo fsck /dev/sdb1
```

> **Important:** Run `fsck` on an **unmounted filesystem** whenever possible to avoid data corruption.

---

## View Mounted Filesystems

```bash
mount
```

or

```bash
findmnt
```

---

# 12. Network Troubleshooting

Many production issues are network-related.

Common problems:

- Cannot reach server
- Slow network
- DNS failures
- Firewall blocks
- Routing issues

---

## Check IP Address

```bash
ip addr
```

---

## Check Routes

```bash
ip route
```

Example:

```text
default via 192.168.1.1
```

---

## Test Connectivity

```bash
ping google.com
```

or

```bash
ping 8.8.8.8
```

If IP works but hostname does not:

Likely a DNS issue.

---

## Check Listening Ports

```bash
ss -tulpn
```

---

## Check Network Interfaces

```bash
ip link
```

---

# Network Troubleshooting Workflow

```text
Application Down
      │
      ▼
Check Interface
      │
      ▼
Check IP Address
      │
      ▼
Check Routing
      │
      ▼
Check DNS
      │
      ▼
Check Firewall
```

---

# 13. DNS Troubleshooting

DNS converts domain names into IP addresses.

---

## Test DNS Resolution

```bash
nslookup google.com
```

or

```bash
dig google.com
```

---

## Check Resolver Configuration

```bash
cat /etc/resolv.conf
```

Example:

```text
nameserver 8.8.8.8
```

---

## Check Host Entries

```bash
cat /etc/hosts
```

---

# Common DNS Problems

- Incorrect nameserver
- Firewall blocking DNS
- DNS server unavailable
- Wrong host entries

---

# 14. SSH Troubleshooting

SSH issues are common in remote server management.

---

## Check Service

```bash
systemctl status ssh
```

or (RHEL-based):

```bash
systemctl status sshd
```

---

## Check Port

```bash
ss -tulpn | grep ssh
```

---

## Verify Configuration

```bash
sudo sshd -t
```

---

## Check Logs

Ubuntu:

```bash
journalctl -u ssh
```

RHEL:

```bash
journalctl -u sshd
```

---

# Common SSH Problems

- Service stopped
- Firewall blocking port 22
- Wrong permissions on SSH keys
- Invalid `sshd_config`
- Authentication failures

---

# 15. Package Manager Troubleshooting

Package installation may fail due to:

- Broken dependencies
- Repository issues
- Network failures
- Locked package database

---

## Ubuntu (APT)

Update repositories:

```bash
sudo apt update
```

Fix broken packages:

```bash
sudo apt --fix-broken install
```

Repair dependencies:

```bash
sudo apt install -f
```

Clean cache:

```bash
sudo apt clean
```

---

## RHEL (DNF)

Refresh metadata:

```bash
sudo dnf makecache
```

Check updates:

```bash
sudo dnf check-update
```

Clean metadata:

```bash
sudo dnf clean all
```

---

# Hands-on Labs

## Lab 1

Monitor CPU:

```bash
top
```

---

## Lab 2

Check memory:

```bash
free -h
```

---

## Lab 3

Find largest directories:

```bash
du -sh /* | sort -h
```

---

## Lab 4

Test network:

```bash
ping 8.8.8.8

ip route
```

---

## Lab 5

Check DNS:

```bash
nslookup openai.com
```

---

## Lab 6

View SSH status:

```bash
systemctl status ssh
```

---

# Chapter Progress

✅ CPU Troubleshooting

✅ Memory Troubleshooting

✅ Disk Troubleshooting

✅ Filesystem Errors

✅ Network Troubleshooting

✅ DNS Troubleshooting

✅ SSH Troubleshooting

✅ Package Manager Troubleshooting

---

---

# 16. Performance Analysis

Performance issues can arise from CPU, memory, disk I/O, or network bottlenecks. Before making changes, identify which resource is under stress.

---

## System Load

Check system load:

```bash
uptime
```

Example:

```text
14:25:18 up 5 days,  2 users,  load average: 1.25, 0.95, 0.82
```

Interpretation:

- 1-minute load
- 5-minute load
- 15-minute load

A consistently high load compared to the number of CPU cores may indicate a bottleneck.

---

## CPU Performance

Monitor CPU:

```bash
top
```

Detailed CPU statistics:

```bash
vmstat 2
```

Example:

```text
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b swpd free buff cache si so bi bo in cs us sy id wa st
 1  0    0 1024  256 4096  0  0  5 10 150 220 12  3 84  1  0
```

Important fields:

- `r` → Runnable processes
- `b` → Blocked processes
- `wa` → I/O wait
- `id` → Idle CPU

---

## Disk I/O Performance

Install `sysstat` if `iostat` is unavailable.

Ubuntu:

```bash
sudo apt install sysstat
```

Check disk statistics:

```bash
iostat -x 2
```

Useful fields:

| Field | Meaning |
|--------|---------|
| r/s | Read operations/sec |
| w/s | Write operations/sec |
| %util | Disk utilization |
| await | Average I/O wait time |

---

## Memory Performance

```bash
free -h
```

View virtual memory:

```bash
vmstat
```

Check top memory consumers:

```bash
ps aux --sort=-%mem | head
```

---

# 17. Boot Failure Recovery

Sometimes Linux fails to boot completely.

Common causes:

- Corrupted GRUB
- Incorrect `/etc/fstab`
- Missing filesystem
- Kernel issues
- Disk failure

---

## Boot into Recovery Mode

Most Linux distributions provide a recovery mode from the GRUB menu.

Recovery mode allows you to:

- Repair filesystems
- Reset passwords
- Fix package issues
- Access a root shell

---

## Incorrect `/etc/fstab`

A wrong entry in `/etc/fstab` can prevent booting.

Example:

```text
UUID=INVALID-UUID /data ext4 defaults 0 2
```

Symptoms:

- Boot hangs
- Emergency mode
- Filesystem errors

Fix:

1. Boot into recovery mode.
2. Edit `/etc/fstab`.
3. Remove or correct the invalid entry.
4. Save and reboot.

---

## Rebuild initramfs

Ubuntu:

```bash
sudo update-initramfs -u
```

RHEL:

```bash
sudo dracut --force
```

---

# 18. Kernel Panic

A **kernel panic** occurs when the Linux kernel encounters a fatal error and cannot continue safely.

Example message:

```text
Kernel panic - not syncing:
Attempted to kill init!
```

---

## Possible Causes

- Corrupted kernel
- Faulty RAM
- Filesystem corruption
- Incompatible kernel module
- Hardware failure

---

## Investigation

View previous boot logs:

```bash
journalctl -b -1
```

Kernel messages:

```bash
dmesg
```

Check hardware health if applicable.

---

# 19. Service Failure Scenarios

## Scenario 1: Web Server Not Responding

Check service:

```bash
systemctl status nginx
```

View logs:

```bash
journalctl -u nginx
```

Test configuration:

```bash
nginx -t
```

Check listening ports:

```bash
ss -tulpn | grep nginx
```

---

## Scenario 2: Database Service Down

Check status:

```bash
systemctl status mysql
```

or

```bash
systemctl status postgresql
```

View logs:

```bash
journalctl -u mysql
```

Common causes:

- Full disk
- Incorrect permissions
- Corrupt configuration
- Port already in use

---

## Scenario 3: Docker Container Keeps Restarting

List containers:

```bash
docker ps -a
```

View logs:

```bash
docker logs <container_name>
```

Inspect container:

```bash
docker inspect <container_name>
```

Common causes:

- Application crash
- Missing environment variables
- Port conflicts
- Insufficient memory

---

# 20. Production Troubleshooting Workflow

When responding to production incidents, follow a consistent workflow.

```text
Alert Triggered
        │
        ▼
Confirm the Issue
        │
        ▼
Assess Impact
        │
        ▼
Collect Logs & Metrics
        │
        ▼
Identify Root Cause
        │
        ▼
Apply Fix
        │
        ▼
Verify Recovery
        │
        ▼
Document Incident
```

---

## Prioritize Based on Impact

| Severity | Description | Response |
|----------|-------------|----------|
| Critical | Entire service unavailable | Immediate |
| High | Major functionality affected | Urgent |
| Medium | Partial degradation | Scheduled |
| Low | Minor issue | Planned |

---

# 21. Monitoring Integration

Troubleshooting becomes easier when monitoring systems are in place.

Common monitoring tools:

- Prometheus
- Grafana
- Node Exporter
- Alertmanager
- Zabbix
- Nagios

Typical metrics:

- CPU usage
- Memory usage
- Disk usage
- Disk I/O
- Network traffic
- Running services
- Application response time

---

# Example Workflow with Prometheus

```text
High CPU Alert
        │
        ▼
Grafana Dashboard
        │
        ▼
Identify High CPU Process
        │
        ▼
Check Logs
        │
        ▼
Fix Application
```

---

# 22. Real-World Case Studies

## Case Study 1: Disk Full

Symptoms:

- Users cannot upload files.
- Applications fail to write logs.

Commands:

```bash
df -h

du -sh /*

find / -type f -size +500M
```

Resolution:

- Remove unnecessary files
- Rotate logs
- Extend storage if required

---

## Case Study 2: High Memory Usage

Symptoms:

- Slow response
- OOM killer messages

Commands:

```bash
free -h

top

journalctl | grep -i oom
```

Resolution:

- Restart leaking application
- Optimize memory usage
- Increase RAM if necessary

---

## Case Study 3: SSH Access Failure

Symptoms:

- Unable to connect remotely

Commands:

```bash
systemctl status ssh

ss -tulpn

journalctl -u ssh
```

Resolution:

- Start SSH service
- Correct firewall rules
- Verify SSH configuration

---

## Case Study 4: DNS Resolution Failure

Symptoms:

- Domain names cannot be resolved.

Commands:

```bash
cat /etc/resolv.conf

nslookup example.com

dig example.com
```

Resolution:

- Correct DNS server configuration
- Verify network connectivity
- Check firewall rules

---

# Hands-on Labs

## Lab 1

Monitor CPU and memory:

```bash
top

free -h
```

---

## Lab 2

View previous boot logs:

```bash
journalctl -b -1
```

---

## Lab 3

Check disk I/O:

```bash
iostat -x 2
```

---

## Lab 4

Inspect running services:

```bash
systemctl --failed
```

---

## Lab 5

Monitor logs in real time:

```bash
journalctl -f
```

---

# Chapter Progress

✅ Performance Analysis

✅ Boot Recovery

✅ Kernel Panic

✅ Service Failure Scenarios

✅ Production Workflow

✅ Monitoring Integration

✅ Real-World Case Studies

---

---

# 23. Incident Documentation

Fixing a problem is only part of the job. Proper documentation ensures that the same issue can be resolved faster in the future.

A good incident report should include:

- Incident ID
- Date and time
- Systems affected
- Severity level
- Symptoms observed
- Root cause
- Resolution steps
- Recovery time
- Preventive actions
- Team members involved

---

## Sample Incident Report

```text
Incident ID: INC-2026-001

Date: 20-Jul-2026

Severity: High

Affected Service:
Nginx Web Server

Symptoms:
Website inaccessible

Root Cause:
Disk full due to application logs

Resolution:
Deleted old logs
Configured logrotate

Downtime:
15 minutes

Preventive Action:
Monitor disk usage
Enable alerting at 80%
```

---

# 24. Root Cause Analysis (RCA)

Root Cause Analysis focuses on identifying **why** an issue happened instead of only fixing it.

---

## The 5 Whys Technique

Example:

**Problem:** Website is down.

**Why?**
Nginx stopped.

**Why?**
Disk became full.

**Why?**
Application logs grew continuously.

**Why?**
Log rotation was not configured.

**Why?**
Server provisioning checklist missed logrotate.

**Root Cause:**
Log rotation was not configured.

---

## Fishbone (Ishikawa) Diagram

```text
                    Problem
                       │
 ┌──────────┬──────────┼──────────┬──────────┐
 │          │          │          │          │
People   Process    Hardware   Software   Network
```

This technique helps categorize possible causes before identifying the actual root cause.

---

# 25. Production Troubleshooting Best Practices

Follow these best practices when working on production systems:

- Stay calm and avoid panic.
- Gather evidence before making changes.
- Verify recent deployments or configuration changes.
- Check monitoring dashboards first.
- Read logs before restarting services.
- Make one change at a time.
- Verify the fix after each change.
- Keep stakeholders informed during major incidents.
- Document everything.

---

## Golden Rules

1. Never assume the cause.
2. Never delete logs before reviewing them.
3. Always back up configuration files before editing.
4. Test configuration changes whenever possible.
5. Verify that the issue is fully resolved.

---

# 26. Troubleshooting Flowcharts

## Service Not Running

```text
Service Down
      │
      ▼
systemctl status
      │
      ▼
Check Logs
      │
      ▼
Validate Configuration
      │
      ▼
Restart Service
      │
      ▼
Verify
```

---

## Disk Full

```text
Disk Full
     │
     ▼
df -h
     │
     ▼
du -sh /*
     │
     ▼
Find Large Files
     │
     ▼
Clean / Archive
     │
     ▼
Verify
```

---

## High CPU

```text
High CPU
     │
     ▼
top
     │
     ▼
Find Process
     │
     ▼
Analyze Logs
     │
     ▼
Fix Application
     │
     ▼
Monitor
```

---

## Network Issue

```text
No Connectivity
        │
        ▼
ip addr
        │
        ▼
ip route
        │
        ▼
ping Gateway
        │
        ▼
Check DNS
        │
        ▼
Check Firewall
```

---

# 27. Troubleshooting Command Cheat Sheet

## System Information

```bash
hostnamectl

uname -a

uptime

date
```

---

## CPU

```bash
top

htop

vmstat

ps aux --sort=-%cpu
```

---

## Memory

```bash
free -h

ps aux --sort=-%mem
```

---

## Disk

```bash
df -h

du -sh *

lsblk

blkid
```

---

## Filesystem

```bash
mount

findmnt

fsck
```

---

## Services

```bash
systemctl status

systemctl restart

systemctl --failed
```

---

## Logs

```bash
journalctl

journalctl -b

journalctl -u nginx

tail -f /var/log/syslog
```

---

## Network

```bash
ip addr

ip route

ping

ss -tulpn

nslookup

dig
```

---

## SSH

```bash
systemctl status ssh

sshd -t
```

---

## Packages

Ubuntu:

```bash
apt update

apt --fix-broken install

apt clean
```

RHEL:

```bash
dnf check-update

dnf clean all
```

---

# 28. Interview Questions

## Beginner

1. What is Linux troubleshooting?
2. What is the first step when troubleshooting an issue?
3. What is the purpose of `journalctl`?
4. What is the difference between `df` and `du`?
5. How do you check running services?

---

## Intermediate

1. Explain the Linux boot process.
2. What causes a kernel panic?
3. How do you troubleshoot high CPU usage?
4. How do you identify memory leaks?
5. What is the purpose of `vmstat`?
6. How do you troubleshoot DNS issues?
7. Explain the role of `systemctl`.

---

## Advanced

1. Walk through your approach to troubleshooting a production outage.
2. Explain Root Cause Analysis (RCA).
3. How would you troubleshoot a server that fails to boot?
4. How do you diagnose intermittent network issues?
5. Describe your incident response process.
6. How would you investigate repeated service crashes?

---

# 29. Scenario-Based Interview Questions

## Scenario 1

A web application is returning **502 Bad Gateway** errors.

What would you check?

Expected approach:

- Verify Nginx service
- Check backend application
- Review logs
- Confirm backend port is listening
- Test configuration

---

## Scenario 2

The root filesystem is 100% full.

Commands:

```bash
df -h

du -sh /*

find / -type f -size +1G
```

---

## Scenario 3

A Linux server cannot resolve domain names.

Commands:

```bash
cat /etc/resolv.conf

nslookup google.com

dig google.com
```

---

## Scenario 4

SSH suddenly stops working.

Commands:

```bash
systemctl status ssh

journalctl -u ssh

ss -tulpn
```

---

## Scenario 5

Users report that the server is extremely slow.

Commands:

```bash
top

free -h

vmstat

iostat -x

df -h
```

---

# 30. Daily Linux Health Checklist

A Linux administrator should routinely verify system health.

### System

- Check uptime
- Verify system load
- Review failed services

### CPU

- Monitor CPU utilization
- Identify unusual spikes

### Memory

- Monitor RAM usage
- Check swap usage

### Storage

- Verify free disk space
- Check inode usage
- Review filesystem health

### Network

- Verify network interfaces
- Check DNS resolution
- Confirm listening ports

### Security

- Review authentication logs
- Check failed login attempts
- Verify firewall status

### Logs

- Review critical system logs
- Monitor application logs
- Ensure log rotation is working

---

# 31. Chapter Summary

Congratulations! 🎉

You have completed the Linux Troubleshooting chapter.

In this chapter, you learned:

- Troubleshooting methodology
- System information gathering
- Linux boot process
- GRUB troubleshooting
- Service troubleshooting
- CPU analysis
- Memory analysis
- Disk troubleshooting
- Filesystem recovery
- Network troubleshooting
- DNS troubleshooting
- SSH troubleshooting
- Package manager troubleshooting
- Performance analysis
- Kernel panic investigation
- Monitoring integration
- Root Cause Analysis (RCA)
- Incident documentation
- Production best practices
- Interview questions
- Command cheat sheet

---

# 32. Revision Notes

Remember these essential commands:

```text
hostnamectl
uname -a
uptime
top
htop
vmstat
free -h
df -h
du -sh
lsblk
mount
findmnt
journalctl
systemctl
ip addr
ip route
ping
ss
nslookup
dig
fsck
```

---

# 📚 Next Chapter

## File Name

```text
18-Linux-Production-Best-Practices.md
```

### Topics Covered

- Production Server Design
- High Availability (HA)
- Backup & Disaster Recovery
- Monitoring & Alerting
- Capacity Planning
- Performance Tuning
- Security Hardening
- Automation
- Documentation Standards
- Deployment Strategies
- Incident Management
- DevOps Best Practices
- Interview Questions
- One-Page Cheat Sheet
