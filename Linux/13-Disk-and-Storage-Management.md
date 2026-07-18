# 💾 Chapter 13 - Disk and Storage Management

> **"Storage is the foundation of every Linux system. Understanding disks, partitions, filesystems, and storage management is essential for every Linux administrator and DevOps engineer."**

---

# 📖 Table of Contents

1. Introduction to Linux Storage
2. Storage Architecture
3. Types of Storage Devices
4. Understanding Block Devices
5. Device Naming in Linux
6. Listing Storage Devices
7. Understanding Partitions
8. MBR vs GPT
9. Primary, Extended and Logical Partitions
10. Partitioning Tools
11. Filesystems
12. Filesystem Hierarchy
13. Creating Filesystems
14. Mounting Filesystems
15. The `/etc/fstab` File
16. Swap Space
17. Storage Monitoring
18. Disk Usage Commands
19. Hands-on Lab
20. Interview Questions
21. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you'll be able to:

- Understand Linux storage architecture
- Identify block devices
- Create and manage partitions
- Understand MBR and GPT
- Create filesystems
- Mount and unmount storage
- Configure persistent mounts
- Manage swap space
- Monitor disk usage

---

# 1. Introduction to Linux Storage

Every Linux system stores data on storage devices.

Examples include:

- HDD (Hard Disk Drive)
- SSD (Solid State Drive)
- NVMe SSD
- USB Flash Drives
- External Hard Drives
- SAN Storage
- NAS Storage

Everything stored on Linux—operating system files, applications, user data, logs, and databases—ultimately resides on storage devices.

---

# Why Storage Management Matters

Proper storage management helps you:

- Prevent disk failures
- Optimize performance
- Avoid running out of disk space
- Protect important data
- Improve system reliability

A poorly managed storage system can lead to:

- Application failures
- Data corruption
- Slow performance
- System crashes

---

# 2. Linux Storage Architecture

```text
                  Applications
                        │
                        ▼
                File System Layer
                        │
                        ▼
                 Block Device Layer
                        │
                        ▼
             Physical Storage Devices
      (HDD / SSD / NVMe / USB / SAN)
```

---

# Storage Layers Explained

### Applications

Applications read and write files.

Examples:

- Nginx
- MySQL
- Docker
- Kubernetes
- User programs

---

### Filesystem

Organizes data into:

- Files
- Directories
- Permissions
- Metadata

Examples:

- ext4
- XFS
- Btrfs

---

### Block Device Layer

Provides access to storage devices in fixed-size blocks.

Linux treats disks as **block devices**.

---

### Physical Storage

Actual hardware storing the data.

Examples:

- SATA HDD
- SATA SSD
- NVMe SSD
- USB Drive

---

# 3. Types of Storage Devices

## Hard Disk Drive (HDD)

Characteristics:

- Mechanical
- Magnetic platters
- Low cost
- High capacity
- Slower performance

Common usage:

- Backup servers
- File servers
- Archives

---

## Solid State Drive (SSD)

Characteristics:

- No moving parts
- Fast read/write
- Low latency
- Silent operation

Used in:

- Laptops
- Cloud servers
- Application servers

---

## NVMe SSD

Uses PCI Express instead of SATA.

Advantages:

- Extremely fast
- Lower latency
- Higher throughput

Common in:

- Modern cloud infrastructure
- Database servers
- Kubernetes worker nodes

---

## USB Storage

Examples:

- Pen drives
- External SSDs
- External HDDs

Useful for:

- Backups
- OS installation
- Data transfer

---

# HDD vs SSD vs NVMe

| Feature | HDD | SSD | NVMe SSD |
|---------|-----|-----|----------|
| Speed | Slow | Fast | Very Fast |
| Moving Parts | Yes | No | No |
| Cost | Low | Medium | High |
| Latency | High | Low | Very Low |
| Common Use | Backup | General Purpose | High Performance |

---

# 4. Understanding Block Devices

Linux accesses storage using **block devices**.

Common examples:

```text
/dev/sda
/dev/sdb
/dev/nvme0n1
/dev/vda
```

Each device represents a physical or virtual disk.

---

# Character Devices vs Block Devices

| Character Device | Block Device |
|------------------|--------------|
| Data one byte at a time | Data in blocks |
| Keyboard | Hard Disk |
| Mouse | SSD |
| Serial Port | USB Storage |

---

# 5. Device Naming in Linux

Linux names storage devices based on their type.

Examples:

### SATA/SCSI

```text
/dev/sda
/dev/sdb
/dev/sdc
```

### NVMe

```text
/dev/nvme0n1
/dev/nvme1n1
```

### Virtual Machines

```text
/dev/vda
/dev/vdb
```

---

# Partition Naming

Examples:

```text
/dev/sda1
/dev/sda2
/dev/sda3
```

NVMe:

```text
/dev/nvme0n1p1
/dev/nvme0n1p2
```

---

# 6. Listing Storage Devices

## lsblk

Shows block devices in a tree format.

```bash
lsblk
```

Example:

```text
NAME        SIZE TYPE MOUNTPOINT
sda         100G disk
├─sda1       50G part /
├─sda2        8G part [SWAP]
└─sda3       42G part /home
```

---

## fdisk

List partitions:

```bash
sudo fdisk -l
```

---

## blkid

Display UUIDs and filesystem types:

```bash
sudo blkid
```

Example:

```text
/dev/sda1 UUID="A12B-34CD" TYPE="ext4"
```

---

## df

Display mounted filesystems:

```bash
df -h
```

---

## du

Display directory sizes:

```bash
du -sh /var/log
```

---

# 7. Understanding Partitions

A **partition** divides a physical disk into separate logical sections.

Example:

```text
100 GB Disk

┌──────────────────────────────┐
│ Partition 1 (50 GB)          │
├──────────────────────────────┤
│ Partition 2 (30 GB)          │
├──────────────────────────────┤
│ Partition 3 (20 GB)          │
└──────────────────────────────┘
```

Each partition can contain:

- A filesystem
- Swap space
- Raw storage

---

# Why Create Partitions?

Advantages:

- Better organization
- Easier backups
- Improved security
- Separate operating systems
- Separate user data

Example layout:

```text
/

/home

/var

Swap
```

This isolates user data from system files.

---

# Chapter Progress

✅ Storage Architecture

✅ Storage Devices

✅ Block Devices

✅ Device Naming

✅ Listing Disks

✅ Partitions

---
---

# 8. MBR vs GPT

Before a disk can be used, it must contain a **partition table**.

A partition table tells the operating system:

- Where each partition starts
- Where each partition ends
- Which partition is bootable
- How many partitions exist

Linux primarily supports two partition table formats:

- MBR (Master Boot Record)
- GPT (GUID Partition Table)

---

# Master Boot Record (MBR)

MBR is the traditional partitioning scheme introduced in the early 1980s.

Characteristics:

- Supports disks up to **2 TB**
- Maximum of **4 primary partitions**
- One partition can be an **extended partition**
- Widely compatible with older systems

Disk Layout:

```text
+-------------------------------+
| Master Boot Record (512 Bytes)|
+-------------------------------+
| Partition Table               |
+-------------------------------+
| Partitions                    |
+-------------------------------+
```

---

# Advantages of MBR

- Simple
- Supported by older BIOS systems
- Compatible with almost all operating systems

---

# Limitations of MBR

- Maximum disk size: **2 TB**
- Only **4 primary partitions**
- Single partition table (no backup)
- Easier to corrupt

---

# GUID Partition Table (GPT)

GPT is the modern partitioning standard and is part of the UEFI specification.

Characteristics:

- Supports disks larger than **2 TB**
- Supports up to **128 partitions** (default)
- Stores multiple copies of partition information
- Includes CRC checks for integrity

Disk Layout:

```text
+-------------------------------+
| Protective MBR                |
+-------------------------------+
| GPT Header                    |
+-------------------------------+
| Partition Entries             |
+-------------------------------+
| User Data                     |
+-------------------------------+
| Backup GPT                    |
+-------------------------------+
```

---

# Advantages of GPT

- Supports very large disks
- More partitions
- Better reliability
- Backup partition table
- Error detection

---

# MBR vs GPT Comparison

| Feature | MBR | GPT |
|----------|-----|-----|
| Maximum Disk Size | 2 TB | 9.4 ZB |
| Primary Partitions | 4 | 128 |
| Backup Table | ❌ No | ✅ Yes |
| Error Detection | ❌ No | ✅ CRC |
| Boot Mode | BIOS | UEFI |

---

# Which One Should You Choose?

Use **GPT** for:

- Modern Linux systems
- SSDs
- NVMe drives
- Cloud servers
- Production environments

Use **MBR** only when supporting legacy BIOS systems.

---

# 9. Primary, Extended and Logical Partitions

### Primary Partition

A primary partition can contain:

- Operating system
- Filesystem
- Boot loader

MBR allows only **4 primary partitions**.

Example:

```text
sda1
sda2
sda3
sda4
```

---

### Extended Partition

To overcome the 4-partition limit, one primary partition can be marked as an **extended partition**.

It acts as a container for logical partitions.

Example:

```text
sda1
sda2
sda3
sda4 (Extended)
```

---

### Logical Partition

Logical partitions exist inside the extended partition.

Example:

```text
sda5
sda6
sda7
```

---

# Partition Layout Example

```text
Disk

├── sda1
├── sda2
├── sda3
└── sda4 (Extended)
      ├── sda5
      ├── sda6
      └── sda7
```

---

# 10. Partitioning Tools

Linux provides several utilities for creating and managing partitions.

Common tools:

- `fdisk`
- `parted`
- `gdisk`
- `cfdisk`

---

# fdisk

The classic partition management tool.

Display disks:

```bash
sudo fdisk -l
```

Start interactive mode:

```bash
sudo fdisk /dev/sdb
```

Useful commands inside `fdisk`:

| Key | Action |
|-----|--------|
| `m` | Help |
| `p` | Print partition table |
| `n` | New partition |
| `d` | Delete partition |
| `w` | Write changes |
| `q` | Quit without saving |

---

# parted

`parted` supports both **MBR** and **GPT**.

Start:

```bash
sudo parted /dev/sdb
```

Display partitions:

```bash
print
```

Create GPT:

```bash
mklabel gpt
```

Create partition:

```bash
mkpart primary ext4 1MiB 100%
```

Exit:

```bash
quit
```

---

# gdisk

`gdisk` is specifically designed for GPT disks.

Start:

```bash
sudo gdisk /dev/sdb
```

Useful for large storage systems.

---

# cfdisk

A user-friendly terminal interface.

Launch:

```bash
sudo cfdisk /dev/sdb
```

Provides a menu-driven interface instead of command prompts.

---

# 11. Filesystems

A filesystem organizes how data is stored and retrieved.

Without a filesystem, Linux cannot store files.

Examples:

- ext4
- XFS
- Btrfs
- FAT32
- NTFS

---

# Common Linux Filesystems

## ext4

The default filesystem for many Linux distributions.

Features:

- Stable
- Fast
- Journaling
- Supports large files

---

## XFS

Designed for high-performance systems.

Advantages:

- Excellent for large files
- High throughput
- Scales well

Commonly used on:

- RHEL
- CentOS
- Enterprise servers

---

## Btrfs

Modern copy-on-write filesystem.

Features:

- Snapshots
- Compression
- Checksums
- RAID support

Commonly used on:

- SUSE Linux
- Advanced storage systems

---

# Filesystem Comparison

| Filesystem | Best For |
|-------------|----------|
| ext4 | General-purpose Linux |
| XFS | Large enterprise workloads |
| Btrfs | Snapshots & advanced storage |
| FAT32 | USB drives |
| NTFS | Windows compatibility |

---

# 12. Creating Filesystems

Before a partition can be used, it must be formatted.

Create an ext4 filesystem:

```bash
sudo mkfs.ext4 /dev/sdb1
```

Create XFS:

```bash
sudo mkfs.xfs /dev/sdb1
```

Create FAT32:

```bash
sudo mkfs.vfat /dev/sdb1
```

⚠️ **Warning:** Formatting a partition erases all existing data.

---

# View Filesystem Information

Display UUID and type:

```bash
sudo blkid
```

Example:

```text
/dev/sdb1 UUID="3f6a..." TYPE="ext4"
```

---

# Chapter Progress

✅ MBR vs GPT

✅ Primary, Extended & Logical Partitions

✅ fdisk

✅ parted

✅ gdisk

✅ cfdisk

✅ Filesystems

✅ Formatting Partitions

---

---

# 13. Mounting Filesystems

A filesystem must be **mounted** before Linux can access its contents.

Mounting means attaching a filesystem to the Linux directory tree.

Example:

```text
Disk
│
└── Partition (/dev/sdb1)
        │
        ▼
Mounted on
        │
        ▼
     /mnt/data
```

After mounting:

```text
/mnt/data
```

becomes the entry point to access the files stored on `/dev/sdb1`.

---

# Mount Points

A mount point is simply a directory.

Examples:

```text
/

 /home

 /mnt

 /media

 /backup

 /data
```

---

# View Mounted Filesystems

```bash
mount
```

or

```bash
findmnt
```

Example:

```text
TARGET     SOURCE
/          /dev/sda1
/home      /dev/sda2
```

---

# Mount a Filesystem

Create a mount point:

```bash
sudo mkdir /mnt/data
```

Mount:

```bash
sudo mount /dev/sdb1 /mnt/data
```

Verify:

```bash
df -h
```

or

```bash
mount
```

---

# Unmounting Filesystems

Remove a mounted filesystem:

```bash
sudo umount /mnt/data
```

or

```bash
sudo umount /dev/sdb1
```

> **Note:** The command is `umount` (without the "n"), not `unmount`.

---

# Common Unmount Errors

### Device is Busy

```text
umount: target is busy
```

Find processes using the mount:

```bash
lsof +D /mnt/data
```

or

```bash
fuser -vm /mnt/data
```

Stop the processes and try again.

---

# 14. Persistent Mounts with `/etc/fstab`

Normally, mounted filesystems disappear after a reboot.

To mount them automatically during boot, configure:

```text
/etc/fstab
```

---

# View fstab

```bash
cat /etc/fstab
```

Example:

```text
UUID=3A9F...   /data   ext4   defaults   0 2
```

---

# fstab Format

```text
<device> <mount_point> <filesystem> <options> <dump> <pass>
```

Example:

```text
UUID=3A9F... /backup ext4 defaults 0 2
```

---

# Why Use UUID?

Instead of:

```text
/dev/sdb1
```

Use:

```text
UUID=3A9F...
```

Reasons:

- Device names may change.
- UUID remains constant.
- More reliable across reboots.

Find UUID:

```bash
blkid
```

---

# Test fstab

Always test changes before rebooting.

```bash
sudo mount -a
```

If there are no errors, the configuration is valid.

---

# 15. Swap Space

Swap is disk space used when physical RAM is exhausted.

```text
RAM Full
   │
   ▼
Swap Space
```

Swap is slower than RAM but prevents applications from crashing due to insufficient memory.

---

# Check Swap

```bash
swapon --show
```

or

```bash
free -h
```

Example:

```text
Swap:
2.0G
```

---

# Create a Swap File

Create a 2 GB file:

```bash
sudo fallocate -l 2G /swapfile
```

Set permissions:

```bash
sudo chmod 600 /swapfile
```

Create swap:

```bash
sudo mkswap /swapfile
```

Enable swap:

```bash
sudo swapon /swapfile
```

Verify:

```bash
swapon --show
```

---

# Make Swap Permanent

Add to `/etc/fstab`:

```text
/swapfile none swap sw 0 0
```

---

# Disable Swap

```bash
sudo swapoff /swapfile
```

---

# 16. Logical Volume Manager (LVM)

LVM provides flexible storage management.

Instead of fixed partitions, LVM allows storage to grow or shrink dynamically.

Architecture:

```text
Physical Disk
      │
      ▼
Physical Volume (PV)
      │
      ▼
Volume Group (VG)
      │
      ▼
Logical Volume (LV)
      │
      ▼
Filesystem
```

---

# LVM Components

### Physical Volume (PV)

A disk or partition initialized for LVM.

```bash
pvcreate /dev/sdb1
```

---

### Volume Group (VG)

A storage pool created from one or more physical volumes.

```bash
vgcreate data_vg /dev/sdb1
```

---

### Logical Volume (LV)

A virtual partition carved out of a volume group.

```bash
lvcreate -L 20G -n data_lv data_vg
```

---

# Display LVM Information

Physical volumes:

```bash
pvs
```

Volume groups:

```bash
vgs
```

Logical volumes:

```bash
lvs
```

---

# Extend a Logical Volume

Increase size:

```bash
sudo lvextend -L +10G /dev/data_vg/data_lv
```

Resize filesystem:

```bash
sudo resize2fs /dev/data_vg/data_lv
```

---

# Advantages of LVM

- Resize volumes without repartitioning
- Combine multiple disks
- Easier storage expansion
- Snapshots (depending on filesystem and LVM support)

---

# 17. RAID Basics

RAID stands for **Redundant Array of Independent Disks**.

It combines multiple disks for:

- Performance
- Redundancy
- Fault tolerance

---

# Common RAID Levels

| RAID | Description | Fault Tolerance |
|------|-------------|-----------------|
| RAID 0 | Striping | ❌ No |
| RAID 1 | Mirroring | ✅ Yes |
| RAID 5 | Striping + Parity | ✅ Yes |
| RAID 6 | Dual Parity | ✅ Yes |
| RAID 10 | Stripe + Mirror | ✅ Yes |

---

# RAID Overview

## RAID 0

```text
Disk A: A1 A3 A5
Disk B: A2 A4 A6
```

Fast, but **no redundancy**.

---

## RAID 1

```text
Disk A: DATA

Disk B: DATA
```

Both disks contain identical data.

---

## RAID 5

Requires at least **3 disks**.

Provides redundancy using parity.

---

## RAID 10

Combines RAID 1 and RAID 0.

- High performance
- High redundancy
- Common in enterprise environments

---

# 18. Storage Monitoring

Check mounted filesystems:

```bash
df -h
```

Directory sizes:

```bash
du -sh /var/*
```

Block devices:

```bash
lsblk
```

Filesystem UUIDs:

```bash
blkid
```

Disk usage summary:

```bash
du -sh *
```

---

# Production Tips

- Keep at least **15–20%** free disk space.
- Use UUIDs in `/etc/fstab`.
- Monitor disk growth regularly.
- Use LVM for servers where storage expansion is expected.
- Verify backups before making partition changes.

---

# Chapter Progress

✅ Mounting

✅ Unmounting

✅ `/etc/fstab`

✅ UUID

✅ Swap Space

✅ LVM

✅ RAID Basics

✅ Storage Monitoring

---

---

# 19. Storage Troubleshooting

Storage-related problems are among the most common issues in Linux servers. A systematic troubleshooting approach helps identify and resolve issues quickly.

---

# Common Storage Problems

- Disk Full
- Filesystem Corruption
- Mount Failure
- Slow Disk Performance
- Swap Issues
- Read-only Filesystem
- Missing Disk
- UUID Changed
- LVM Errors
- RAID Failure

---

# Scenario 1: Disk Full

Symptoms:

```text
No space left on device
```

Check disk usage:

```bash
df -h
```

Find large directories:

```bash
du -sh /*
```

Find large files:

```bash
find / -type f -size +500M
```

Delete unnecessary files or archive old logs.

---

# Scenario 2: Filesystem Won't Mount

Check filesystem:

```bash
lsblk -f
```

Try mounting manually:

```bash
sudo mount /dev/sdb1 /mnt/data
```

Check filesystem health:

```bash
sudo fsck /dev/sdb1
```

---

# Scenario 3: Read-Only Filesystem

Symptoms:

```text
Read-only file system
```

Check mount options:

```bash
mount | grep sdb1
```

Remount as read-write:

```bash
sudo mount -o remount,rw /mnt/data
```

---

# Scenario 4: High Disk Usage

Display usage:

```bash
df -h
```

Find largest directories:

```bash
du -sh /* | sort -h
```

Clean:

- Cache
- Temporary files
- Old logs
- Unused packages

---

# Scenario 5: Missing Disk

Check devices:

```bash
lsblk
```

Kernel messages:

```bash
dmesg
```

List disks:

```bash
fdisk -l
```

---

# Scenario 6: Swap Not Working

Check swap:

```bash
swapon --show
```

Enable:

```bash
sudo swapon /swapfile
```

Verify:

```bash
free -h
```

---

# Scenario 7: LVM Volume Full

Display logical volumes:

```bash
lvs
```

Extend:

```bash
sudo lvextend -L +10G /dev/vg/data
```

Resize filesystem:

```bash
sudo resize2fs /dev/vg/data
```

---

# 20. Useful Storage Commands

## Block Devices

```bash
lsblk
```

---

## UUID

```bash
blkid
```

---

## Mounted Filesystems

```bash
mount
```

---

## Filesystem Usage

```bash
df -h
```

---

## Directory Size

```bash
du -sh *
```

---

## Filesystem Check

```bash
fsck /dev/sdb1
```

---

## Partition Table

```bash
fdisk -l
```

---

## Storage Statistics

```bash
iostat
```

---

## Swap

```bash
swapon --show
```

---

## LVM

```bash
pvs
vgs
lvs
```

---

# 21. Best Practices

## Partitioning

- Use GPT for modern systems.
- Separate `/home`, `/var`, and `/tmp` for production servers.
- Avoid creating unnecessary partitions.

---

## Filesystems

- Use **ext4** for general Linux systems.
- Use **XFS** for enterprise workloads.
- Use **Btrfs** when snapshots are required.

---

## Mounting

- Always use UUIDs in `/etc/fstab`.
- Test using:

```bash
sudo mount -a
```

before rebooting.

---

## Storage Monitoring

Regularly monitor:

- Disk usage
- Inode usage
- Filesystem health
- Disk performance
- SMART status

---

## Backup

Before:

- Formatting
- Partitioning
- Resizing
- LVM changes

Always create backups.

---

# 22. Hands-on Labs

## Lab 1: View Storage Devices

```bash
lsblk
```

---

## Lab 2: View UUIDs

```bash
blkid
```

---

## Lab 3: Monitor Disk Usage

```bash
df -h
```

---

## Lab 4: Check Directory Size

```bash
du -sh /var/*
```

---

## Lab 5: Mount a Disk

```bash
mkdir /mnt/demo

sudo mount /dev/sdb1 /mnt/demo
```

---

## Lab 6: Unmount

```bash
sudo umount /mnt/demo
```

---

## Lab 7: View Swap

```bash
free -h

swapon --show
```

---

## Lab 8: View LVM

```bash
pvs

vgs

lvs
```

---

# 23. Interview Questions

## Beginner

1. What is a partition?
2. What is a filesystem?
3. Difference between `df` and `du`?
4. What is swap?
5. What is `/etc/fstab`?

---

## Intermediate

1. Explain MBR vs GPT.
2. Difference between HDD and SSD.
3. Explain mounting.
4. Why use UUID instead of `/dev/sdb1`?
5. Explain `lsblk`.

---

## Advanced

1. Explain LVM architecture.
2. What is RAID?
3. How do you troubleshoot a disk full issue?
4. How do you extend an LVM volume?
5. How would you recover a failed mount after reboot?

---

# 24. One-Page Cheat Sheet

## Disk Information

```bash
lsblk
fdisk -l
blkid
```

---

## Filesystem

```bash
df -h
du -sh *
mount
findmnt
```

---

## Mount

```bash
mount /dev/sdb1 /mnt/data

umount /mnt/data
```

---

## fstab

```bash
cat /etc/fstab

mount -a
```

---

## Filesystem Creation

```bash
mkfs.ext4 /dev/sdb1

mkfs.xfs /dev/sdb1
```

---

## Swap

```bash
swapon --show

swapoff /swapfile
```

---

## LVM

```bash
pvs

vgs

lvs

lvextend
```

---

## Monitoring

```bash
df -h

du -sh *

iostat

lsblk
```

---

# 25. Chapter Summary

Congratulations! 🎉

You now understand the complete Linux storage stack.

In this chapter, you learned:

- Linux storage architecture
- HDD, SSD, and NVMe
- Block devices
- Device naming
- MBR vs GPT
- Primary, Extended, and Logical partitions
- `fdisk`, `parted`, `gdisk`, `cfdisk`
- Filesystems (ext4, XFS, Btrfs)
- Formatting disks
- Mounting and unmounting
- `/etc/fstab`
- UUIDs
- Swap space
- LVM
- RAID basics
- Storage monitoring
- Troubleshooting
- Best practices
- Hands-on labs
- Interview questions
- Cheat sheet

---

# 📚 Next Chapter

## File Name

```text
16-Linux-Security.md
```

### Topics Covered

- Linux Security Fundamentals
- Users & Authentication
- Password Policies
- File Permissions Review
- sudo Configuration
- SSH Security
- Firewall (`ufw` & `firewalld`)
- SELinux
- AppArmor
- Fail2Ban
- File Integrity Monitoring
- Security Auditing
- Best Practices
- Hands-on Labs
- Interview Questions
- Cheat Sheet
