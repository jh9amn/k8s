# 🔐 Chapter 5 - File Permissions and Ownership

> **"Linux security starts with permissions."**

Every file, directory, device, process, socket, and configuration file in Linux is protected by a permission system.

Whenever you encounter errors like:

```
Permission denied

Access denied

Operation not permitted
```

it almost always means Linux's permission system is preventing the operation.

Understanding permissions is one of the most important skills for Linux Administrators, DevOps Engineers, Cloud Engineers, Security Engineers, and Kubernetes Administrators.

---

# 📖 Table of Contents

1. Introduction
2. Why Permissions Exist
3. Linux Security Model
4. Ownership
5. Users, Groups and Others
6. Understanding `ls -l`
7. File Types
8. Read Permission
9. Write Permission
10. Execute Permission
11. Internal Working
12. DevOps Connection

---

# 🎯 Learning Objectives

After completing this chapter, you'll be able to:

✅ Read Linux permissions

✅ Modify permissions safely

✅ Change ownership

✅ Understand permission errors

✅ Fix Docker volume permission issues

✅ Fix Kubernetes PersistentVolume permission issues

✅ Understand SUID, SGID and Sticky Bit

✅ Understand ACLs

---

# 1. Introduction

Linux is a **multi-user operating system**.

Imagine a company server.

```
                    Linux Server

      ┌──────────────┬──────────────┬──────────────┐

      │              │              │

   Aman           Rahul          Priya

 Developer        Tester        Manager
```

Suppose everyone can read, modify and delete every file.

What would happen?

```
Aman deletes Rahul's project.

↓

Rahul edits Priya's salary file.

↓

Priya removes system configuration.

↓

Entire server becomes unusable.
```

Linux prevents this using a **permission model**.

Every resource belongs to

- an owner
- a group
- everyone else

and each category has different permissions.

---

# 2. Why Permissions Exist

Permissions provide

- Security
- Isolation
- Data Protection
- Multi-user Collaboration
- Process Separation

Without permissions,

servers would be impossible to manage securely.

Example

```
Payroll.pdf

Owner:
HR

Others:
No Access
```

Only HR can read the file.

---

# 3. Linux Security Model

Every object in Linux has

```
Owner

↓

Group

↓

Others
```

Each category receives

```
Read

Write

Execute
```

The kernel checks these permissions every time a process attempts to access a file.

---

## Example

```
report.pdf

Owner

↓

Aman

Group

↓

Developers

Others

↓

Everyone Else
```

Permissions:

```
Owner

Read
Write

Group

Read

Others

No Access
```

---

# 4. Ownership

Every file has an owner.

View ownership using

```bash
ls -l
```

Example

```
-rw-r--r--

1

aman

developers

1024

Jul 18

notes.txt
```

Here

Owner

```
aman
```

Group

```
developers
```

---

## Why Ownership Matters

Suppose

```
deployment.yaml
```

belongs to

```
root
```

Regular users cannot modify it.

This prevents accidental changes to production deployments.

---

# 5. Understanding Owner, Group and Others

Linux divides permissions into **three classes**.

```
               File

                 │

     ┌───────────┼────────────┐

     │           │            │

   Owner       Group       Others
```

---

## Owner

Usually the creator of the file.

Example

```
Aman

↓

notes.txt
```

Owner permissions apply only to Aman.

---

## Group

Multiple users can belong to one group.

Example

```
developers

├── Aman

├── Rahul

└── Priya
```

Instead of assigning permissions individually,

Linux grants permissions to

```
developers
```

Every member receives them.

---

## Others

Everyone else on the system.

Example

```
Owner

↓

Aman

Group

↓

Developers

Others

↓

Every remaining user
```

---

# 6. Understanding `ls -l`

The most important command.

```bash
ls -l
```

Example

```
-rwxr-xr--

1 aman developers

1250 Jul 18

deploy.sh
```

Let's decode it.

```
-rwxr-xr--
```

contains **10 characters**.

---

## Character 1

Represents file type.

```
-

Regular File
```

```
d

Directory
```

```
l

Symbolic Link
```

```
c

Character Device
```

```
b

Block Device
```

```
p

Named Pipe
```

```
s

Socket
```

---

## Remaining Nine Characters

Grouped into

```
Owner

Group

Others
```

Example

```
rwx

r-x

r--
```

---

Visual representation

```
rwx | r-x | r--

Owner

Group

Others
```

---

# 7. File Types

Linux doesn't only store files.

Everything is treated as a file.

| Symbol | Type |
|---------|------|
| `-` | Regular File |
| `d` | Directory |
| `l` | Symbolic Link |
| `b` | Block Device |
| `c` | Character Device |
| `p` | Named Pipe |
| `s` | Socket |

Example

```
drwxr-xr-x

Projects
```

First character

```
d
```

means

Directory.

---

# 8. Read Permission (`r`)

Binary Value

```
4
```

Meaning

The file can be read.

Example

```
notes.txt
```

Permission

```
r--
```

Allowed

✅ Open

✅ Read

✅ Copy

Not Allowed

❌ Modify

❌ Delete (depends on directory permissions)

---

## Directory Read Permission

Read permission on directories means

```
List Contents
```

Example

```bash
ls
```

works only if the directory has

```
r
```

permission.

---

# 9. Write Permission (`w`)

Binary Value

```
2
```

Allows

```
Modify File

Append Data

Overwrite Data
```

Example

```
rw-
```

means

Read

Write

No Execute

---

## Directory Write Permission

Allows

```
Create Files

Delete Files

Rename Files
```

inside the directory.

This is an important interview question.

Many beginners think write permission only modifies existing files.

For directories,

it controls directory entries.

---

# 10. Execute Permission (`x`)

Binary Value

```
1
```

Allows execution.

Example

```
script.sh
```

Without execute permission

```bash
./script.sh
```

Result

```
Permission denied
```

Grant execute permission

```bash
chmod +x script.sh
```

Now

```bash
./script.sh
```

works.

---

## Execute Permission on Directories

This confuses many beginners.

Execute on a directory means

```
Traversal Permission
```

Without execute permission

You cannot

```
cd directory
```

even if you have read permission.

Example

```
drw-------
```

You can list the directory,

but cannot enter it.

---

# Internal Working

Suppose

```
cat notes.txt
```

is executed.

```
User

↓

Shell

↓

Kernel

↓

Check UID

↓

Check File Owner

↓

Check Group

↓

Check Others

↓

Permission Match?

↓

YES

↓

Open File

↓

Read Data

↓

Display Output
```

If permissions fail

```
Permission Denied
```

The kernel performs this permission check **every time** a file or directory is accessed.

---

# 🐳 Docker Connection

One of the most common Docker issues is:

```
Permission denied
```

Example:

```bash
docker run -v $(pwd):/app ubuntu
```

If the user inside the container has a different UID/GID than the host file owner, the container may not be able to read or write mounted files.

A common fix is to:
- Match container UID/GID with the host user.
- Use a non-root user inside the image.
- Set correct ownership using `chown`.

---

# ☸️ Kubernetes Connection

Permission problems also occur with Persistent Volumes.

Example:

```
Pod

↓

Volume

↓

Permission denied
```

Solution:

```yaml
securityContext:
  runAsUser: 1001
  fsGroup: 1001
```

`runAsUser` controls the process UID, while `fsGroup` ensures mounted volumes have appropriate group ownership.

---

# 💡 Quick Recap

- Every file has an **Owner**, **Group**, and **Others**.
- Each category has **Read (4)**, **Write (2)**, and **Execute (1)** permissions.
- Use `ls -l` to inspect permissions.
- Execute permission on a directory allows **traversal**, not execution.
- The Linux kernel checks permissions on every file access.

---

📌 **To be continued in Part 2...**

Next, we'll cover:

- `chmod` (Symbolic Mode)
- `chmod` (Numeric Mode)
- Common permission values (644, 755, 600, 777...)
- Recursive permissions
- `chown`
- `chgrp`
- `umask`
- Internal permission calculations
- Production examples
