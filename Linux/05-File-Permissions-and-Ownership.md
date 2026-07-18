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

---

# 11. chmod - Changing File Permissions

One of the most frequently used Linux commands is **`chmod` (Change Mode)**.

It changes the permissions of files and directories.

## Syntax

```bash
chmod [options] permissions file
```

Example:

```bash
chmod 755 script.sh
```

or

```bash
chmod u+x script.sh
```

---

# Two Ways to Use chmod

Linux supports two methods.

```
chmod

├── Symbolic Mode

└── Numeric (Octal) Mode
```

We'll learn both.

---

# 12. Symbolic Mode

Instead of using numbers, symbolic mode uses letters.

### Users

| Symbol | Meaning |
|---------|----------|
| u | User (Owner) |
| g | Group |
| o | Others |
| a | All |

---

### Operations

| Symbol | Meaning |
|---------|---------|
| + | Add permission |
| - | Remove permission |
| = | Set exact permission |

---

### Permissions

| Symbol | Meaning |
|---------|----------|
| r | Read |
| w | Write |
| x | Execute |

---

## Examples

### Give execute permission to owner

```bash
chmod u+x script.sh
```

Before

```
rw-r--r--
```

After

```
rwxr--r--
```

---

### Remove write permission

```bash
chmod g-w file.txt
```

---

### Give read permission to others

```bash
chmod o+r report.txt
```

---

### Give execute permission to everyone

```bash
chmod a+x install.sh
```

---

### Make file read-only

```bash
chmod a-w file.txt
```

---

### Set exact permissions

```bash
chmod u=rwx,g=rx,o=r file.txt
```

Result

```
rwxr-xr--
```

---

# 13. Numeric (Octal) Permissions

This is the method most commonly used in production.

Instead of letters,

Linux converts permissions into numbers.

---

## Binary Representation

```
Read

4

Write

2

Execute

1
```

Linux simply adds them.

---

### Example

```
Read

4

+

Write

2

+

Execute

1

=

7
```

So

```
rwx

=

7
```

---

Another example

```
Read

4

+

Write

2

=

6
```

```
rw-

=

6
```

---

```
Read

4

+

Execute

1

=

5
```

```
r-x

=

5
```

---

```
Read

4

=

4
```

```
r--
```

---

```
No Permission

=

0
```

```
---
```

---

# Permission Table

| Number | Permission |
|----------|------------|
| 7 | rwx |
| 6 | rw- |
| 5 | r-x |
| 4 | r-- |
| 3 | -wx |
| 2 | -w- |
| 1 | --x |
| 0 | --- |

---

# Understanding 755

Command

```bash
chmod 755 script.sh
```

Breakdown

```
7

↓

Owner

↓

rwx

5

↓

Group

↓

r-x

5

↓

Others

↓

r-x
```

Visual

```
rwx

r-x

r-x
```

---

# Understanding 644

```
6

↓

rw-

4

↓

r--

4

↓

r--
```

Result

```
rw-r--r--
```

Most configuration files use

```
644
```

Examples

```
nginx.conf

deployment.yaml

README.md
```

---

# Understanding 600

```
rw-------

```

Only owner can access.

Commonly used for

```
SSH Keys

Secrets

Passwords
```

---

# Understanding 700

```
rwx------
```

Owner has complete access.

Nobody else can access.

---

# Understanding 777

```
rwxrwxrwx
```

Everyone

Can

Read

Write

Execute

⚠️ Avoid using **777** unless absolutely necessary.

It creates serious security risks.

---

# Most Common Permission Values

| Permission | Meaning | Typical Usage |
|------------|---------|---------------|
| 644 | Owner RW, Others R | Configuration files |
| 600 | Owner only | Private keys, secrets |
| 755 | Executable files and directories | Scripts, binaries |
| 700 | Private directories | Personal scripts |
| 775 | Shared team directories | Collaboration |
| 664 | Shared editable files | Team documents |

---

# 14. Recursive Permissions

To apply permissions to an entire directory tree:

```bash
chmod -R 755 Project
```

Linux changes:

```
Project

↓

Every Folder

↓

Every File
```

---

## Production Example

Suppose:

```
Website

├── css

├── js

├── images

└── index.html
```

Grant permissions

```bash
chmod -R 755 Website
```

---

# 15. chown - Change Ownership

Permissions alone are not enough.

Sometimes ownership itself is incorrect.

Linux provides

```
chown
```

---

## Syntax

```bash
sudo chown owner file
```

Example

```bash
sudo chown aman report.txt
```

Now

```
Owner

↓

aman
```

---

## Change Owner and Group

```bash
sudo chown aman:developers app.py
```

Result

Owner

```
aman
```

Group

```
developers
```

---

## Recursive Ownership

```bash
sudo chown -R aman:developers Project
```

Changes ownership of every file and directory under `Project`.

---

# Internal Working

```
User

↓

chown

↓

Kernel

↓

Update Inode Metadata

↓

Owner Changed
```

Notice:

The file data is not modified—only metadata (owner/group) stored in the inode changes.

---

# 16. chgrp - Change Group

Sometimes you only want to change the group.

Syntax

```bash
sudo chgrp developers report.txt
```

Now

```
Owner

↓

Unchanged

Group

↓

developers
```

---

# Difference Between chown and chgrp

| Command | Changes |
|----------|----------|
| chown | Owner (and optionally group) |
| chgrp | Group only |

---

# 17. Default Permissions

When you create a new file,

does Linux always create

```
777?
```

No.

Linux calculates default permissions.

Typical defaults are:

Files

```
666
```

Directories

```
777
```

Then Linux subtracts the **umask** value.

---

# 18. umask

`umask` defines which permissions are removed from newly created files and directories.

View current umask:

```bash
umask
```

Example output

```
0022
```

---

## How Linux Calculates Permissions

### File

Maximum

```
666
```

umask

```
022
```

Calculation

```
666

-

022

=

644
```

Result

```
rw-r--r--
```

---

### Directory

Maximum

```
777

-

022

=

755
```

Result

```
rwxr-xr-x
```

---

## Change umask Temporarily

```bash
umask 027
```

New files:

```
640
```

New directories:

```
750
```

---

# Production Example

Many organizations set:

```bash
umask 027
```

Reason:

- Others receive **no permissions**.
- Group has limited access.
- Better security for shared servers.

---

# Common Mistakes

❌ Giving `777` to solve every permission issue.

❌ Using `chmod -R 777 /`.

❌ Forgetting that directory permissions behave differently from file permissions.

❌ Confusing ownership (`chown`) with permissions (`chmod`).

---

# Best Practices

✅ Follow the Principle of Least Privilege.

✅ Prefer `755` for executable scripts.

✅ Use `644` for configuration files.

✅ Keep private keys at `600`.

✅ Change ownership instead of making everything writable.

---
---

# 19. Special Permissions

Linux provides three **special permissions** beyond the standard `rwx` model.

```
Special Permissions

├── SUID
├── SGID
└── Sticky Bit
```

These are widely used in production systems and are common interview topics.

---

# 20. SUID (Set User ID)

Normally, a program runs with the permissions of the user executing it.

With **SUID**, the program runs with the permissions of the **file owner**.

## Example

```bash
ls -l /usr/bin/passwd
```

Output:

```text
-rwsr-xr-x
```

Notice the `s`:

```
rws
```

This means the SUID bit is set.

### Why?

Changing a password modifies:

```text
/etc/shadow
```

Only `root` can modify this file.

When a normal user runs:

```bash
passwd
```

the program temporarily executes with the permissions of its owner (`root`), allowing the password change.

---

## Setting SUID

```bash
chmod u+s filename
```

Numeric form:

```bash
chmod 4755 filename
```

The leading `4` represents the SUID bit.

---

# 21. SGID (Set Group ID)

SGID has two behaviors.

## On Executable Files

Programs execute with the **group permissions** of the file's group.

Set it using:

```bash
chmod g+s program
```

Numeric:

```bash
chmod 2755 program
```

---

## On Directories

This is the most common use.

New files created inside the directory inherit the directory's group.

Example:

```
Project/

Group: developers
```

All new files inside automatically belong to the `developers` group.

This is extremely useful for team collaboration.

---

# 22. Sticky Bit

The Sticky Bit is mainly used on shared directories.

Without it:

```
User A

↓

Creates file

↓

User B

↓

Deletes it
```

With the Sticky Bit:

Only the file owner, directory owner, or `root` can delete the file.

---

## Example

```bash
ls -ld /tmp
```

Output:

```text
drwxrwxrwt
```

Notice the `t` at the end.

The `/tmp` directory is writable by everyone, but users cannot delete each other's files.

---

## Set Sticky Bit

```bash
chmod +t shared
```

Numeric:

```bash
chmod 1777 shared
```

The leading `1` represents the Sticky Bit.

---

# Summary of Special Permissions

| Permission | Numeric | Symbol | Purpose |
|------------|---------|--------|---------|
| SUID | 4 | s | Run as file owner |
| SGID | 2 | s | Inherit group / Run as group |
| Sticky Bit | 1 | t | Prevent unauthorized deletion |

---

# 23. Access Control Lists (ACLs)

Traditional permissions allow access for only:

- Owner
- Group
- Others

But what if you want to give access to a specific additional user?

Example:

```
Owner: Aman
Group: Developers
```

You also want user `Rahul` to have write access.

Standard permissions can't express this.

ACLs solve the problem.

---

# View ACL

```bash
getfacl report.txt
```

Example output:

```text
user::rw-
user:rahul:rwx
group::r--
other::---
```

---

# Add ACL

```bash
setfacl -m u:rahul:rwx report.txt
```

Meaning:

- `u` → user
- `rahul`
- `rwx`

---

# Remove ACL

```bash
setfacl -x u:rahul report.txt
```

---

# Remove All ACLs

```bash
setfacl -b report.txt
```

---

# Why ACLs Matter

ACLs are useful when:

- One user needs temporary access.
- Cross-team collaboration.
- Shared project directories.
- File servers.

---

# 24. How Linux Checks Permissions Internally

Suppose you execute:

```bash
cat report.txt
```

The kernel performs the following checks:

```
Application

↓

System Call

↓

Kernel

↓

Get Process UID/GID

↓

Read Inode Metadata

↓

Are you Owner?

↓

Yes → Check Owner Permission

↓

No

↓

Are you in File Group?

↓

Yes → Check Group Permission

↓

No

↓

Check Others

↓

Permission Granted?

↓

YES

↓

Access File

↓

NO

↓

Permission Denied
```

This decision happens for **every file access**.

---

# 25. Docker Permission Issues

A common Docker problem:

```text
Permission denied
```

### Cause

Host file:

```
UID 1000
```

Container process:

```
UID 1001
```

The container doesn't own the file.

---

## Debugging

```bash
whoami
id
ls -ln
```

`ls -ln` displays numeric UIDs and GIDs.

---

## Fix

```dockerfile
USER appuser
```

Or:

```bash
chown -R appuser:appuser /app
```

---

# 26. Kubernetes Permission Issues

A common error:

```text
Permission denied
```

while writing to a Persistent Volume.

### Cause

The process UID doesn't match the volume ownership.

---

## Solution

```yaml
securityContext:
  runAsUser: 1001
  runAsGroup: 1001
  fsGroup: 1001
```

`fsGroup` ensures mounted volumes are accessible to the process.

---

# 27. Production Scenarios

## Scenario 1

SSH refuses a private key:

```text
Permissions 0644 for 'id_rsa' are too open
```

Fix:

```bash
chmod 600 ~/.ssh/id_rsa
```

---

## Scenario 2

Shell script won't execute:

```text
Permission denied
```

Fix:

```bash
chmod +x deploy.sh
```

---

## Scenario 3

Nginx cannot read a configuration file.

Check:

```bash
ls -l /etc/nginx
```

Correct ownership or permissions:

```bash
sudo chown root:root nginx.conf
sudo chmod 644 nginx.conf
```

---

## Scenario 4

Application cannot write logs.

Investigate:

```bash
ls -ld /var/log/myapp
```

Adjust ownership:

```bash
sudo chown appuser:appuser /var/log/myapp
```

---

# 28. Hands-on Lab

## Create a Test File

```bash
touch demo.txt
```

---

## View Permissions

```bash
ls -l demo.txt
```

---

## Change Permissions

```bash
chmod 755 demo.txt
```

---

## Remove Execute Permission

```bash
chmod a-x demo.txt
```

---

## Add Execute Permission

```bash
chmod +x demo.txt
```

---

## Change Owner

```bash
sudo chown $USER:$USER demo.txt
```

---

## Create Shared Directory

```bash
mkdir shared
chmod 2775 shared
```

Observe that new files inherit the directory's group.

---

## Experiment with ACLs

```bash
setfacl -m u:testuser:rwx demo.txt
getfacl demo.txt
```

---

# 29. Interview Questions

## Beginner

1. What are Linux file permissions?
2. Explain `r`, `w`, and `x`.
3. What does `chmod` do?
4. Difference between `chmod` and `chown`?
5. Explain permission `755`.

---

## Intermediate

1. Why is `644` commonly used for configuration files?
2. Why should private keys use `600`?
3. What is `umask`?
4. Difference between symbolic and numeric permissions?
5. How does `chmod -R` work?

---

## Advanced

1. Explain SUID with an example.
2. Explain SGID on directories.
3. Explain the Sticky Bit.
4. How does the Linux kernel evaluate permissions?
5. When would you choose ACLs over standard permissions?
6. How would you debug a Docker volume permission issue?
7. How does `fsGroup` solve Kubernetes PersistentVolume permission problems?

---

# 30. One-Page Cheat Sheet

| Command | Purpose |
|---------|---------|
| `ls -l` | View permissions |
| `chmod 755 file` | Set numeric permissions |
| `chmod u+x file` | Add execute permission for owner |
| `chmod -R 755 dir` | Change permissions recursively |
| `chown user file` | Change owner |
| `chown user:group file` | Change owner and group |
| `chgrp group file` | Change group |
| `umask` | View default permission mask |
| `chmod u+s file` | Set SUID |
| `chmod g+s dir` | Set SGID |
| `chmod +t dir` | Set Sticky Bit |
| `getfacl file` | View ACL |
| `setfacl -m u:user:rwx file` | Add ACL |
| `setfacl -x u:user file` | Remove ACL |
| `id` | Show UID and GIDs |
| `ls -ln` | Show numeric owner/group IDs |

---

# 31. Common Mistakes

❌ Using `chmod 777` to "fix" every problem.

❌ Running containers as `root`.

❌ Forgetting execute permission on scripts.

❌ Editing sensitive files with incorrect ownership.

❌ Ignoring `umask` settings.

❌ Forgetting that deleting a file depends on the **directory's** permissions, not the file's.

---

# 32. Best Practices

- Follow the **Principle of Least Privilege**.
- Use `600` for private keys.
- Use `644` for configuration files.
- Use `755` for executable scripts and directories.
- Prefer changing ownership (`chown`) instead of granting excessive permissions.
- Avoid `777` in production.
- Use ACLs for fine-grained access control.
- Run Docker and Kubernetes workloads as non-root users whenever possible.

---

# 33. Chapter Summary

In this chapter, you learned:

- The Linux permission model (`Owner`, `Group`, `Others`).
- How to read permissions using `ls -l`.
- The meaning of `r`, `w`, and `x`.
- Symbolic and numeric permissions.
- `chmod`, `chown`, `chgrp`, and `umask`.
- Special permissions: **SUID**, **SGID**, and **Sticky Bit**.
- ACLs with `getfacl` and `setfacl`.
- How the Linux kernel checks permissions internally.
- Common Docker and Kubernetes permission issues.
- Real-world production troubleshooting techniques.
- Best practices, interview questions, and a quick reference.
