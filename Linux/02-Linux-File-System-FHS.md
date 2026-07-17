# 📚 Chapter 2 - Linux File System (FHS)

> **"Everything in Linux is a File."**
>
> One of the biggest differences between Linux and Windows is the way files are organized.
>
> Linux follows a standard called the **Filesystem Hierarchy Standard (FHS)**, which defines where system files, configuration files, user files, logs, binaries, and applications should be stored.

---

# 📖 Table of Contents

1. What is a File System?
2. What is FHS?
3. Linux Directory Structure
4. Root Directory (/)
5. Important Directories
6. Absolute vs Relative Paths
7. Path Traversal
8. Hidden Files
9. File Types in Linux
10. Inodes
11. Symbolic Links vs Hard Links
12. Mount Points
13. Best Practices
14. Useful Commands
15. Interview Questions
16. Quick Revision

---

# What is a File System?

A **File System** is the method an operating system uses to:

- Store files
- Organize directories
- Retrieve data
- Manage permissions
- Track file locations

Without a file system:

```
Hard Disk

↓

Random Data

↓

Impossible to Locate Files
```

The file system organizes everything in a structured way.

---

# Everything is a File

One of Linux's core philosophies is:

> **Everything is a file.**

Examples:

| Resource | File Example |
|----------|--------------|
| Text | notes.txt |
| Folder | /home |
| Hard Disk | /dev/sda |
| USB Drive | /dev/sdb |
| Keyboard | /dev/input/* |
| Terminal | /dev/tty |
| Running Process Info | /proc |
| System Information | /sys |

Even hardware devices are represented as files.

---

# What is FHS?

FHS stands for:

**Filesystem Hierarchy Standard**

It defines **where different types of files should live**.

This makes Linux distributions consistent.

For example:

```
Ubuntu

↓

/etc

↓

Configuration Files
```

```
CentOS

↓

/etc

↓

Configuration Files
```

No matter the distribution, the purpose of `/etc` remains the same.

---

# Linux Directory Tree

```
/

├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── srv
├── sys
├── tmp
├── usr
└── var
```

Everything starts from the **root directory (`/`)**.

---

# Root Directory (/)

The root directory is the **top-level directory**.

```
/

↓

Everything Starts Here
```

Unlike Windows:

```
C:\

D:\

E:\
```

Linux has **one unified directory tree**.

All storage devices are mounted somewhere under `/`.

---

# /bin

**Binary Executables**

Contains essential commands required by all users.

Examples:

```
ls

cp

mv

cat

echo

pwd
```

Check:

```bash
ls /bin
```

---

# /sbin

**System Binaries**

Contains administrative commands.

Examples:

```
fdisk

mkfs

reboot

shutdown
```

Mostly intended for system administration.

---

# /boot

Stores files required during system startup.

Contains:

- Linux Kernel
- GRUB Bootloader
- Initial RAM filesystem (initramfs)

Example:

```bash
ls /boot
```

---

# /dev

Contains **device files**.

Examples:

```
/dev/sda

/dev/sdb

/dev/null

/dev/random

/dev/tty
```

Everything from disks to terminals appears here.

---

# /etc

Contains **system configuration files**.

Examples:

```
/etc/passwd

/etc/group

/etc/hosts

/etc/fstab

/etc/ssh/
```

You will edit this directory frequently as a Linux administrator.

---

# /home

Stores personal files for normal users.

Example:

```
/home/

├── aman

├── john

└── alice
```

Each user gets a separate home directory.

---

# /root

The home directory of the **root user**.

```
/root
```

Do not confuse this with the root directory `/`.

---

# /usr

Stores user-space applications and shared resources.

Contains:

```
/usr/bin

/usr/lib

/usr/share

/usr/local
```

Many installed programs live here.

---

# /var

Stores **variable data**.

Examples:

```
Logs

Databases

Cache

Mail

Temporary runtime files
```

Important examples:

```
/var/log

/var/cache

/var/spool

/var/tmp
```

---

# /tmp

Temporary files.

Anyone can write here.

Many distributions automatically clean it during reboot.

---

# /opt

Optional third-party software.

Examples:

```
Google Chrome

Oracle

Custom Enterprise Applications
```

---

# /proc

A **virtual filesystem**.

Contains information about:

- Running processes
- CPU
- Memory
- Kernel

Example:

```bash
cat /proc/cpuinfo

cat /proc/meminfo
```

Files are generated dynamically by the kernel.

---

# /sys

Another virtual filesystem.

Contains information about:

- Devices
- Drivers
- Kernel objects

Used for advanced hardware and kernel management.

---

# /media

Automatically mounted removable devices.

Examples:

```
USB Drive

DVD

External Hard Disk
```

---

# /mnt

Used for **temporary manual mounts**.

Example:

```bash
sudo mount /dev/sdb1 /mnt
```

---

# /run

Stores runtime information.

Examples:

- Process IDs (PID)
- Sockets
- Runtime state

Cleared after reboot.

---

# /srv

Stores data served by system services.

Examples:

- Web server content
- FTP files

Not every distribution uses it heavily.

---

# Directory Summary

| Directory | Purpose |
|------------|----------|
| / | Root of the filesystem |
| /bin | Essential user commands |
| /sbin | System administration commands |
| /boot | Bootloader and kernel |
| /dev | Device files |
| /etc | Configuration files |
| /home | User home directories |
| /root | Root user's home |
| /usr | Applications and libraries |
| /var | Logs and variable data |
| /tmp | Temporary files |
| /proc | Process and kernel information |
| /sys | Hardware and kernel interface |
| /media | Removable media |
| /mnt | Temporary mount point |
| /run | Runtime files |
| /opt | Optional software |
| /srv | Service data |

---

# Absolute vs Relative Paths

### Absolute Path

Starts from the root (`/`).

Example:

```bash
/home/aman/Documents/file.txt
```

Works regardless of your current directory.

---

### Relative Path

Starts from your current location.

Example:

```bash
Documents/file.txt
```

Depends on where you are.

---

# Path Traversal

```
.

Current Directory

..

Parent Directory

~

Home Directory

/

Root Directory
```

Examples:

```bash
pwd

cd ..

cd .

cd ~

cd /
```

---

# Hidden Files

Files beginning with a dot (`.`) are hidden.

Example:

```
.bashrc

.gitconfig

.profile

.ssh
```

Show them with:

```bash
ls -la
```

---

# File Types in Linux

| Symbol | Type |
|---------|------|
| - | Regular File |
| d | Directory |
| l | Symbolic Link |
| c | Character Device |
| b | Block Device |
| s | Socket |
| p | Named Pipe (FIFO) |

Example:

```bash
ls -l
```

---

# Inodes

An **inode** stores metadata about a file.

It contains:

- Owner
- Permissions
- Size
- Timestamps
- Disk block locations

It **does not store the filename**.

View inode numbers:

```bash
ls -i
```

---

# Hard Link vs Symbolic Link

| Hard Link | Symbolic Link |
|------------|---------------|
| Shares inode | New inode |
| Survives original filename removal | Breaks if original target is deleted |
| Same filesystem only | Can span filesystems |

Create links:

```bash
ln file.txt hardlink.txt

ln -s file.txt symlink.txt
```

---

# Mount Points

Linux attaches storage devices into the directory tree.

Example:

```
Disk

↓

/dev/sdb1

↓

Mounted At

↓

/mnt/data
```

Check mounted filesystems:

```bash
mount

df -h
```

---

# Useful Commands

Show current directory:

```bash
pwd
```

List files:

```bash
ls
```

Long listing:

```bash
ls -l
```

Show hidden files:

```bash
ls -la
```

Display directory tree (if installed):

```bash
tree
```

Display disk usage:

```bash
df -h
```

Display directory size:

```bash
du -sh /var/log
```

Show mounted filesystems:

```bash
mount
```

---

# Best Practices

✅ Store personal files under `/home`.

✅ Do not modify system files unless necessary.

✅ Avoid writing permanent data to `/tmp`.

✅ Keep configuration files in `/etc`.

✅ Use absolute paths in automation scripts where possible.

---

# Interview Questions

## Beginner

1. What is the Linux File System?
2. What is FHS?
3. What is the root directory?
4. Difference between `/` and `/root`?
5. What is stored in `/etc`?

---

## Intermediate

6. Explain the purpose of `/proc`.
7. What is `/sys`?
8. Difference between `/bin` and `/usr/bin`?
9. Difference between `/media` and `/mnt`?
10. Explain inodes.

---

## Advanced

11. What is a mount point?
12. Difference between a hard link and a symbolic link?
13. Why does Linux represent devices as files?
14. How does the kernel expose process information?
15. Why is FHS important in system administration?

---

# 📝 Quick Revision

- Linux follows the **Filesystem Hierarchy Standard (FHS)**.
- Everything starts from the **root directory (`/`)**.
- `/etc` stores configuration.
- `/home` stores user data.
- `/var` stores logs and variable data.
- `/proc` and `/sys` are virtual filesystems.
- Hidden files start with `.`.
- Inodes store file metadata.
- Hard links share an inode; symbolic links reference another path.
- Mount points integrate storage devices into the directory tree.
