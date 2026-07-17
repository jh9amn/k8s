# 📚 Chapter 3 - Files and Directories

> **"Everything in Linux is a file."**
>
> Understanding how Linux stores and manages files is one of the most important skills for every Linux Administrator, DevOps Engineer, Cloud Engineer, and Site Reliability Engineer (SRE).
>
> In this chapter, you'll learn how to navigate the Linux file system, create and manage files and directories, search for them efficiently, and understand what happens internally when these operations are performed.

---

# 📖 Table of Contents

1. Introduction
2. Files vs Directories
3. Understanding Paths
4. Absolute vs Relative Paths
5. File Navigation Commands
   - pwd
   - ls
   - cd
6. Creating Files & Directories
   - touch
   - mkdir
7. Copying Files
   - cp
8. Moving & Renaming Files
   - mv
9. Removing Files & Directories
   - rm
   - rmdir
10. Viewing File Information
11. Hidden Files
12. Wildcards
13. Internal Working
14. DevOps Connection
15. Production Examples
16. Common Mistakes
17. Best Practices
18. Hands-on Lab
19. Interview Questions
20. Quick Revision

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Navigate any Linux filesystem confidently.
- Understand the difference between files and directories.
- Work with absolute and relative paths.
- Create, copy, move, rename, and delete files.
- Understand hidden files and wildcards.
- Search and inspect files.
- Apply these concepts in Docker, Kubernetes, and cloud environments.

---

# 1. Introduction

Every Linux system is built around files and directories.

Whether you're editing a Kubernetes manifest, reading Docker logs, configuring Nginx, or writing a Bash script—you are interacting with files.

Unlike Windows, Linux organizes everything under a **single directory tree** that starts at the **root directory (`/`)**.

```
                    /
                    │
     ┌──────────────┼──────────────┐
     │              │              │
   /home          /etc          /var
     │                            │
     │                            │
   user1                       log files
```

As a DevOps engineer, you'll spend most of your day navigating this tree.

---

# 2. Files vs Directories

Many beginners think files and directories are completely different. Internally, a **directory is a special type of file** that stores references to other files and directories.

## File

A file stores information.

Examples:

```text
notes.txt
resume.pdf
deployment.yaml
Dockerfile
script.sh
```

Files can contain:

- Text
- Images
- Videos
- Configuration
- Source Code
- Binary Data

---

## Directory

A directory organizes files.

Think of it as a folder in Windows.

Example:

```text
Documents/

Pictures/

Downloads/

Projects/
```

Example hierarchy:

```text
Projects/

├── Docker/

│   ├── Dockerfile

│   └── docker-compose.yml

├── Kubernetes/

│   ├── deployment.yaml

│   └── service.yaml

└── Terraform/

    └── main.tf
```

---

## Why Directories Exist

Without directories:

```
100000 Files

↓

One Location

↓

Impossible to Manage
```

Directories provide:

- Organization
- Faster lookup
- Easier permission management
- Logical grouping

---

# 3. Understanding Paths

A path tells Linux where a file or directory is located.

Imagine a postal address.

```
Country

↓

State

↓

City

↓

Street

↓

House Number
```

Linux follows the same principle.

```
/

↓

home

↓

aman

↓

Projects

↓

Docker

↓

Dockerfile
```

Complete path:

```bash
/home/aman/Projects/Docker/Dockerfile
```

---

# 4. Absolute vs Relative Paths

## Absolute Path

An absolute path always begins from the root directory (`/`).

Example:

```bash
/etc/nginx/nginx.conf
```

Another example:

```bash
/home/aman/Documents/resume.pdf
```

Advantages:

- Works from any location.
- Ideal for scripts.
- Removes ambiguity.

Production example:

```bash
cp config.yaml /etc/myapp/
```

No matter where you execute this command, Linux knows the exact destination.

---

## Relative Path

A relative path starts from the current working directory.

Suppose your current directory is:

```bash
/home/aman
```

Command:

```bash
cd Projects
```

Linux interprets it as:

```bash
/home/aman/Projects
```

Relative paths are shorter and convenient for interactive work.

---

## Comparison

| Absolute Path | Relative Path |
|---------------|---------------|
| Starts with `/` | Starts from current directory |
| Works everywhere | Depends on current location |
| Preferred in scripts | Convenient in terminal |

---

# 5. File Navigation Commands

Before manipulating files, you need to know where you are and how to move around.

The three most fundamental commands are:

- `pwd`
- `ls`
- `cd`

Mastering these commands is essential.

---

# 5.1 pwd (Print Working Directory)

Displays your current directory.

Syntax:

```bash
pwd
```

Example:

```bash
$ pwd

/home/aman
```

### Internal Working

```
User

↓

Shell (bash)

↓

Kernel

↓

Current Working Directory

↓

Display Path
```

The shell asks the kernel for the process's current working directory and prints the result.

### DevOps Example

You're connected to a Kubernetes control plane node.

```
$ pwd

/etc/kubernetes/manifests
```

Now you immediately know you're editing static Pod manifests.

---

# 5.2 ls (List Directory Contents)

Lists files and directories.

Syntax:

```bash
ls
```

Example:

```bash
$ ls

Desktop
Documents
Downloads
Pictures
Projects
```

---

## Long Listing

```bash
ls -l
```

Example:

```text
-rw-r--r-- 1 aman users 2548 Jul 18 notes.txt
```

Breakdown:

```text
-rw-r--r--

↓

Permissions

↓

Owner

↓

Group

↓

Size

↓

Modified Date

↓

Filename
```

---

## Show Hidden Files

```bash
ls -a
```

Output:

```text
.
..
.bashrc
.profile
.gitconfig
```

Files beginning with `.` are hidden.

---

## Human Readable

```bash
ls -lh
```

Instead of:

```
1048576
```

You'll see:

```
1.0M
```

---

## Common Options

| Command | Description |
|----------|-------------|
| ls | List files |
| ls -l | Long listing |
| ls -a | Show hidden files |
| ls -lh | Human-readable sizes |
| ls -R | Recursive listing |
| ls -t | Sort by modification time |
| ls -S | Sort by file size |

---

# 5.3 cd (Change Directory)

Moves between directories.

Syntax:

```bash
cd directory_name
```

Example:

```bash
cd Documents
```

---

## Home Directory

```bash
cd
```

or

```bash
cd ~
```

Both commands return you to your home directory.

---

## Parent Directory

```bash
cd ..
```

Example:

Current:

```text
/home/aman/Documents
```

After:

```bash
cd ..
```

Result:

```text
/ home/aman
```

---

## Root Directory

```bash
cd /
```

Moves directly to the root of the filesystem.

---

## Previous Directory

```bash
cd -
```

Very useful during system administration.

Example:

```bash
cd /etc

cd /var/log

cd -
```

Returns to:

```text
/ etc
```

---

## Navigation Diagram

```text
/

├── home

│    └── aman

│          ├── Documents

│          ├── Projects

│          └── Downloads

└── etc
```

Commands:

```bash
cd ~

↓

/home/aman

cd Projects

↓

/home/aman/Projects

cd ..

↓

/home/aman

cd /

↓

Root Directory
```

---

# 💡 Pro Tip

Use **Tab Completion** to save time.

Instead of typing:

```bash
cd Docum
```

Press **Tab**.

Linux automatically completes:

```bash
cd Documents
```

Benefits:

- Faster navigation
- Fewer typing errors
- Discover available files

---
