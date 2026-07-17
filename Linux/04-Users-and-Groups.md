# 👥 Chapter 4 - Users and Groups in Linux

> **"Linux is a multi-user operating system."**

One of the biggest differences between Linux and many desktop operating systems is that Linux was designed from the beginning to allow multiple users to work on the same system simultaneously.

Every file, process, and service in Linux belongs to a user and a group. Understanding users and groups is essential for managing permissions, security, system administration, and DevOps environments.

---

# 📖 Table of Contents

1. Introduction
2. Why Linux Uses Users and Groups
3. Multi-user Architecture
4. Types of Users
5. Root User
6. System Users
7. Regular Users
8. User IDs (UID)
9. Group IDs (GID)
10. Primary vs Secondary Groups
11. Understanding `/etc/passwd`
12. Common User Commands
13. Internal Working
14. DevOps Examples
15. Best Practices
16. Interview Questions
17. Quick Revision

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Linux's multi-user architecture.
- Differentiate between root, system, and regular users.
- Understand UIDs and GIDs.
- Read and interpret the `/etc/passwd` file.
- Use commands to identify users and groups.
- Understand why ownership is critical in Linux and DevOps.

---

# 1. Introduction

Unlike Windows, Linux was designed to allow **multiple users** to access the same machine at the same time.

Imagine a company server.

```
                    Linux Server
                         │
        ┌────────────────┼────────────────┐
        │                │                │
      Aman             Rahul           Admin
     (Developer)      (Tester)        (Root)
```

Each user has:

- Their own username.
- Their own password.
- Their own home directory.
- Their own permissions.
- Their own running processes.

This isolation ensures that one user cannot accidentally or intentionally interfere with another user's files or processes.

---

# 2. Why Linux Uses Users and Groups

Suppose there are three employees working on the same server.

```
Aman
Rahul
Priya
```

If Linux didn't have user management:

```
Everyone could:

❌ Delete each other's files.

❌ Kill each other's processes.

❌ Modify system configurations.

❌ Read confidential data.
```

This would be insecure and chaotic.

Instead, Linux associates every resource with an owner and a group.

Example:

```
resume.pdf

Owner: aman

Group: developers
```

Only users with the appropriate permissions can access or modify the file.

---

# 3. Multi-user Architecture

Linux supports multiple users working simultaneously.

```
                 Applications

        User A    User B    User C

              │      │      │

              └──────┼──────┘

                     │

                 Linux Kernel

                     │

                 Hardware
```

The Linux kernel manages:

- User authentication
- Process isolation
- File permissions
- Resource allocation
- Security boundaries

This architecture is one of the reasons Linux is widely used on servers.

---

# 4. Types of Users

Linux classifies users into three main categories:

```
Users

├── Root User

├── System Users

└── Regular Users
```

Let's understand each one.

---

# 4.1 Root User

The **root user** is the superuser.

It has unrestricted access to the entire system.

Characteristics:

- Can read any file.
- Can modify any configuration.
- Can install or remove software.
- Can create or delete users.
- Can start or stop services.
- Can change file ownership and permissions.

Username:

```
root
```

Typical UID:

```
0
```

Example:

```bash
sudo apt update
```

The `sudo` command temporarily grants root privileges to an authorized user.

⚠️ **Warning:** Avoid logging in directly as `root` for everyday tasks. Use `sudo` instead to reduce the risk of accidental system-wide changes.

---

# 4.2 System Users

System users are created automatically for services and applications.

Examples:

```
www-data

mysql

postgres

nginx

sshd

systemd-network
```

These users:

- Usually do not have login shells.
- Own service files and processes.
- Improve security by isolating services.

Example:

The Nginx web server runs as:

```
www-data
```

If Nginx is compromised, the attacker gains the privileges of `www-data`, not `root`.

---

# 4.3 Regular Users

Regular users are human users.

Examples:

```
aman

john

alice

developer1
```

A regular user typically has:

- A home directory (`/home/username`)
- A login shell
- Limited permissions

Example:

```
/home/aman
```

This directory stores:

- Documents
- Downloads
- SSH keys
- Configuration files
- Projects

---

# 5. User IDs (UID)

Internally, Linux identifies users using **User IDs (UIDs)** rather than usernames.

Example:

```
Username: aman

UID: 1001
```

The kernel checks the UID, not the username, when making permission decisions.

### Common UID Ranges

| UID Range | Purpose |
|------------|---------|
| 0 | Root user |
| 1–999 | System users (distribution-dependent) |
| 1000+ | Regular users |

You can view your UID using:

```bash
id
```

Example:

```bash
$ id

uid=1000(aman) gid=1000(aman) groups=1000(aman),27(sudo)
```

---

# 6. Group IDs (GID)

A **group** is a collection of users.

Instead of assigning permissions to every user individually, Linux allows permissions to be granted to groups.

Example:

```
developers

├── aman

├── rahul

└── priya
```

If the `developers` group has write access to a project directory, all members inherit that access.

Groups are identified by a **Group ID (GID)**.

Example:

```
Group: developers

GID: 1002
```

---

# 7. Primary vs Secondary Groups

Every user has:

- **One Primary Group**
- **Zero or More Secondary Groups**

Example:

```
User: aman

Primary Group:
developers

Secondary Groups:
docker
sudo
kubernetes
```

Why is this useful?

Suppose:

- The `docker` group grants permission to use Docker.
- The `sudo` group grants administrative privileges.
- The `kubernetes` group grants access to cluster configuration.

Instead of changing file permissions repeatedly, you simply add users to the appropriate groups.

---

# 8. Understanding `/etc/passwd`

Every Linux system stores user account information in:

```text
/etc/passwd
```

Despite its name, **this file does not store passwords** on modern Linux systems.

Display its contents:

```bash
cat /etc/passwd
```

Sample entry:

```text
aman:x:1000:1000:Aman Kumar:/home/aman:/bin/bash
```

Each field is separated by a colon (`:`).

```
Username : Password Placeholder : UID : GID : Comment : Home Directory : Login Shell
```

Let's break it down:

| Field | Meaning |
|--------|---------|
| `aman` | Username |
| `x` | Password placeholder (actual password stored in `/etc/shadow`) |
| `1000` | User ID (UID) |
| `1000` | Primary Group ID (GID) |
| `Aman Kumar` | Comment / Full name (GECOS field) |
| `/home/aman` | Home directory |
| `/bin/bash` | Default login shell |

---

# 9. Useful User Information Commands

## `whoami`

Displays the current logged-in user.

```bash
whoami
```

Example:

```text
aman
```

---

## `id`

Displays UID, GID, and group memberships.

```bash
id
```

Example:

```text
uid=1000(aman)
gid=1000(aman)
groups=1000(aman),27(sudo),999(docker)
```

---

## `who`

Shows users currently logged into the system.

```bash
who
```

Example:

```text
aman     pts/0    Jul 18 09:30
rahul    pts/1    Jul 18 09:45
```

---

## `w`

Displays logged-in users and what they are doing.

```bash
w
```

Example output includes:

- Username
- Login time
- Idle time
- Current command

This is useful for monitoring active users on a server.

---

---

# 10. Understanding `/etc/shadow`

Earlier, we learned that `/etc/passwd` stores basic information about users.

But where are passwords stored?

👉 The answer is:

```text
/etc/shadow
```

Unlike `/etc/passwd`, this file is **highly protected**.

Only the **root user** (or users with appropriate privileges) can read it.

---

## Why was `/etc/shadow` introduced?

In early UNIX systems, encrypted passwords were stored inside `/etc/passwd`.

Since every user needs to read `/etc/passwd` (to resolve usernames, home directories, shells, etc.), everyone could also see the encrypted passwords.

Although the passwords were hashed, attackers could copy the hashes and attempt offline brute-force attacks.

To improve security:

- `/etc/passwd` kept only user information.
- Password hashes were moved to `/etc/shadow`.

---

## View Shadow File

```bash
sudo cat /etc/shadow
```

Example:

```text
aman:$y$j9T$wB4...f8P:19890:0:99999:7:::
```

---

## Fields of `/etc/shadow`

```
Username

↓

Password Hash

↓

Last Password Change

↓

Minimum Days

↓

Maximum Days

↓

Warning Days

↓

Inactive Days

↓

Expiration Date
```

Example:

```text
aman:$6$abcxyz...:19890:0:99999:7:::
```

| Field | Meaning |
|--------|----------|
| aman | Username |
| $6$abcxyz | Password Hash |
| 19890 | Last password change |
| 0 | Minimum password age |
| 99999 | Maximum password age |
| 7 | Warn user before expiry |

---

## Password Hashes

Linux never stores plaintext passwords.

Instead:

```
Password

↓

Hash Algorithm

↓

Password Hash

↓

Stored in /etc/shadow
```

Example hash types:

```
$1$

MD5

$5$

SHA-256

$6$

SHA-512
```

Modern distributions may use stronger hashing algorithms like **yescrypt**.

---

# 11. Understanding `/etc/group`

Groups are stored inside:

```text
/etc/group
```

View:

```bash
cat /etc/group
```

Example:

```text
docker:x:999:aman
```

Meaning:

| Field | Description |
|--------|-------------|
| docker | Group Name |
| x | Password Placeholder |
| 999 | Group ID |
| aman | Members |

---

Example:

```
developers

├── Aman

├── Rahul

└── Priya
```

Instead of giving permissions individually,

Linux grants permissions to:

```
developers
```

Everyone inside automatically inherits them.

---

# 12. Understanding `/etc/gshadow`

Similar to `/etc/shadow`.

Stores:

- Group passwords
- Group administrators

Normally administrators rarely edit this file manually.

---

# 13. Creating Users

Linux provides multiple commands.

---

# useradd

Low-level command.

Syntax:

```bash
sudo useradd username
```

Example

```bash
sudo useradd aman
```

Creates the account.

However,

by default,

it may **not create a home directory** on every distribution.

---

## Create Home Directory

```bash
sudo useradd -m aman
```

Result

```
/home/aman
```

is created automatically.

---

## Specify Login Shell

```bash
sudo useradd -m -s /bin/bash aman
```

---

## Specify UID

```bash
sudo useradd -u 2001 aman
```

Useful in enterprise environments.

---

## Internal Working

```
useradd

↓

Update /etc/passwd

↓

Update /etc/shadow

↓

Update /etc/group

↓

Create Home Directory

↓

Copy Skeleton Files

↓

Done
```

---

# What are Skeleton Files?

When Linux creates:

```
/home/aman
```

it copies files from:

```text
/etc/skel
```

Example:

```
.bashrc

.profile

.bash_logout
```

Every new user automatically receives these files.

---

# adduser

Many Debian-based systems provide:

```bash
sudo adduser aman
```

Unlike `useradd`,

this is **interactive**.

Example:

```
Password:

Full Name:

Phone Number:

Room Number:
```

It creates:

- User
- Home Directory
- Group

Automatically.

---

# useradd vs adduser

| useradd | adduser |
|-----------|----------|
| Low-level | High-level |
| Non-interactive | Interactive |
| More control | Easier for beginners |
| Available on most Linux systems | Mainly Debian/Ubuntu |

---

# 14. Modifying Users

Linux uses:

```
usermod
```

---

## Change Username

```bash
sudo usermod -l aman_new aman
```

---

## Change Home Directory

```bash
sudo usermod -d /home/newhome aman
```

---

## Move Home Directory

```bash
sudo usermod -m -d /home/newhome aman
```

---

## Change Login Shell

```bash
sudo usermod -s /bin/zsh aman
```

---

## Add User to Group

```bash
sudo usermod -aG docker aman
```

This is one of the most common DevOps commands.

After running it:

```
aman

↓

docker group

↓

Docker commands work without sudo
```

⚠ Never forget the **-a** option.

Incorrect:

```bash
sudo usermod -G docker aman
```

This replaces all existing supplementary groups.

Correct:

```bash
sudo usermod -aG docker aman
```

---

# 15. Deleting Users

Delete account:

```bash
sudo userdel aman
```

Home directory remains.

---

Delete user and home directory:

```bash
sudo userdel -r aman
```

Deletes:

```
Account

↓

Home Directory

↓

Mail Spool
```

Use carefully.

---

# 16. Managing Groups

Create group

```bash
sudo groupadd developers
```

---

Delete group

```bash
sudo groupdel developers
```

---

Rename group

```bash
sudo groupmod -n backend developers
```

---

View groups

```bash
groups
```

Example:

```
aman

docker

sudo

developers
```

---

View a user's groups

```bash
groups aman
```

---

# 17. Managing Passwords

Linux uses:

```bash
passwd
```

---

Change your own password

```bash
passwd
```

---

Change another user's password

```bash
sudo passwd aman
```

---

Lock Account

```bash
sudo passwd -l aman
```

Account becomes locked.

---

Unlock Account

```bash
sudo passwd -u aman
```

---

Expire Password

```bash
sudo passwd -e aman
```

User must change password at next login.

---

# 18. Password Aging

Command:

```bash
chage
```

Display password policy

```bash
sudo chage -l aman
```

Example:

```
Password expires

90 Days

Warning

7 Days

Inactive

30 Days
```

Set maximum age

```bash
sudo chage -M 90 aman
```

Set minimum age

```bash
sudo chage -m 7 aman
```

Set warning period

```bash
sudo chage -W 10 aman
```

This is commonly enforced in enterprise environments for security compliance.

---
