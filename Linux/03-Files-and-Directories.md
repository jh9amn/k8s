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
---

# 6. Creating Files and Directories

Until now, we've learned how to navigate the Linux file system. Now it's time to create our own files and folders.

Every software project begins with creating directories and files.

For example:

```
MyProject/

├── app.py

├── Dockerfile

├── docker-compose.yml

├── README.md

└── kubernetes/

    ├── deployment.yaml

    └── service.yaml
```

Linux provides several commands to create and organize them efficiently.

---

# 6.1 touch Command

## What is `touch`?

The `touch` command is primarily used to:

- Create a new empty file.
- Update the timestamp of an existing file.

Syntax:

```bash
touch <filename>
```

Example:

```bash
touch notes.txt
```

Result:

```
notes.txt
```

An empty file named `notes.txt` is created.

---

## Create Multiple Files

```bash
touch file1.txt file2.txt file3.txt
```

Result:

```
file1.txt

file2.txt

file3.txt
```

---

## Create Different Types of Files

```bash
touch Dockerfile

touch README.md

touch deployment.yaml

touch app.py
```

Useful when starting a new project.

---

## Update Timestamp

Suppose:

```
notes.txt
```

already exists.

Running

```bash
touch notes.txt
```

does **not** erase its contents.

Instead, Linux updates:

- Last Access Time (atime)
- Last Modified Time (mtime)

We'll study timestamps in detail later.

---

## Internal Working

When you execute:

```bash
touch notes.txt
```

the shell requests the kernel to:

1. Check whether the file exists.
2. If not, create a new inode.
3. Allocate a directory entry.
4. Update metadata.
5. Save timestamps.

```
User

↓

Shell

↓

Kernel

↓

Filesystem

↓

Create Inode

↓

Directory Entry

↓

Done
```

---

## Production Example

Create Kubernetes manifests.

```bash
touch deployment.yaml

touch service.yaml

touch ingress.yaml
```

---

# 6.2 mkdir Command

## What is mkdir?

`mkdir` means **Make Directory**.

Syntax:

```bash
mkdir directory_name
```

Example:

```bash
mkdir Projects
```

Result:

```
Projects/
```

---

## Create Multiple Directories

```bash
mkdir Docker Kubernetes Terraform
```

Output:

```
Docker/

Kubernetes/

Terraform/
```

---

## Nested Directories

Normally:

```bash
mkdir Projects/Docker
```

fails if

```
Projects
```

doesn't exist.

Instead use

```bash
mkdir -p Projects/Docker
```

Result

```
Projects/

└── Docker/
```

The `-p` option creates parent directories automatically.

---

## Create an Entire Project Structure

```bash
mkdir -p DevOps/{Docker,Kubernetes,Terraform,Ansible}
```

Result:

```
DevOps

├── Docker

├── Kubernetes

├── Terraform

└── Ansible
```

This is extremely useful when starting a new project.

---

## Internal Working

```
mkdir

↓

Shell

↓

Kernel

↓

Allocate Inode

↓

Create Directory Entry

↓

Add "." and ".."

↓

Done
```

Every directory automatically contains:

```
.

..

```

`.`

Current directory.

`..`

Parent directory.

---

# 7. Copying Files

Linux uses the `cp` command.

Syntax:

```bash
cp source destination
```

---

## Copy a File

```bash
cp notes.txt backup.txt
```

Result

```
notes.txt

backup.txt
```

Both files now exist.

---

## Copy to Another Directory

```bash
cp notes.txt Documents/
```

---

## Copy Multiple Files

```bash
cp file1.txt file2.txt Backup/
```

---

## Copy Directories

Directories require the recursive option.

```bash
cp -r Project Backup
```

Result

```
Backup/

└── Project/
```

---

## Preserve Permissions

```bash
cp -p file.txt backup.txt
```

Preserves:

- Ownership
- Permissions
- Timestamp

Useful during server migrations.

---

## Internal Working

```
cp

↓

Open Source File

↓

Read Data

↓

Create Destination

↓

Write Data

↓

Close Files
```

Unlike `mv`, copying duplicates data.

---

# 8. Moving and Renaming Files

Linux uses the `mv` command.

Syntax

```bash
mv source destination
```

---

## Rename a File

```bash
mv old.txt new.txt
```

Result

```
old.txt

↓

new.txt
```

---

## Move File

```bash
mv notes.txt Documents/
```

---

## Move Multiple Files

```bash
mv *.txt Backup/
```

---

## Rename Directory

```bash
mv Project DevOpsProject
```

---

## Internal Working

When moving inside the **same filesystem**:

Linux simply updates the directory entry.

No data is copied.

```
Directory A

↓

Change Pointer

↓

Directory B
```

Extremely fast.

---

### Moving Across Different Filesystems

Suppose

```
Disk A

↓

Disk B
```

Linux performs:

```
Copy

↓

Verify

↓

Delete Original
```

This is why cross-device moves take longer.

---

# 9. Removing Files

Deleting files is powerful—and dangerous.

Unlike Windows, there is no Recycle Bin by default.

Once deleted, recovery may be difficult.

---

# rm Command

Syntax

```bash
rm filename
```

Example

```bash
rm notes.txt
```

---

## Delete Multiple Files

```bash
rm file1.txt file2.txt
```

---

## Delete Using Wildcards

```bash
rm *.log
```

Deletes all `.log` files in the current directory.

Always verify with:

```bash
ls *.log
```

before deleting.

---

## Interactive Delete

```bash
rm -i notes.txt
```

Linux asks:

```
Remove regular file?

(y/n)
```

---

## Force Delete

```bash
rm -f notes.txt
```

No confirmation.

Use with caution.

---

## Delete Directory

```bash
rm -r Project
```

Deletes:

```
Project/

↓

Everything Inside
```

---

## Force Recursive Delete

```bash
rm -rf Project
```

One of the most dangerous Linux commands.

It recursively removes a directory and all its contents without confirmation.

⚠️ Double-check the path before using `rm -rf`.

---

# 9.1 rmdir Command

`rmdir` removes **only empty directories**.

Syntax:

```bash
rmdir EmptyFolder
```

If the directory contains files:

```bash
rmdir Project
```

Output:

```
Directory not empty
```

This safety behavior makes `rmdir` useful when you specifically want to ensure you're not deleting data accidentally.

---

# 🧠 Quick Comparison

| Command | Purpose | Deletes Non-Empty Directory? |
|---------|---------|------------------------------|
| `rm file.txt` | Delete a file | N/A |
| `rm -r dir` | Delete a directory recursively | ✅ Yes |
| `rm -rf dir` | Force delete recursively | ✅ Yes |
| `rmdir dir` | Delete an empty directory | ❌ No |

---

# 💡 Production Tip

Before deleting anything on a production server:

```bash
pwd
ls -la
```

Verify:

- Your current directory
- The files you're about to remove

A mistaken `rm -rf` in the wrong location can cause serious outages.

---

# 10. Finding and Inspecting Files

As a DevOps engineer, you will frequently search for configuration files, log files, certificates, Dockerfiles, and Kubernetes manifests. Linux provides several commands for locating and identifying files.

---

# 10.1 find Command

## What is `find`?

The `find` command searches for files and directories recursively within a specified path.

### Syntax

```bash
find <path> <options>
```

---

## Find by Name

```bash
find . -name "notes.txt"
```

Example:

```
.

├── Documents

│   └── notes.txt
```

Output:

```
./Documents/notes.txt
```

---

## Case-Insensitive Search

```bash
find . -iname "dockerfile"
```

Matches:

```
Dockerfile

dockerfile

DOCKERFILE
```

---

## Find Directories Only

```bash
find . -type d
```

---

## Find Files Only

```bash
find . -type f
```

---

## Find YAML Files

```bash
find . -name "*.yaml"
```

Useful when working with Kubernetes manifests.

---

## Find Large Files

```bash
find /var/log -size +100M
```

Searches for files larger than 100 MB.

---

## Find Recently Modified Files

```bash
find . -mtime -1
```

Shows files modified within the last 24 hours.

---

## Execute a Command on Results

Delete all `.log` files:

```bash
find . -name "*.log" -delete
```

Or:

```bash
find . -name "*.tmp" -exec rm {} \;
```

⚠ Always verify the results before deleting.

---

# 10.2 locate Command

Unlike `find`, `locate` uses a pre-built database, making searches much faster.

Example:

```bash
locate nginx.conf
```

Output:

```
/etc/nginx/nginx.conf
```

If newly created files don't appear, update the database:

```bash
sudo updatedb
```

### `find` vs `locate`

| Feature | find | locate |
|----------|------|--------|
| Search Method | Scans filesystem | Uses database |
| Speed | Slower | Very Fast |
| Real-time | Yes | Depends on updated database |

---

# 10.3 which Command

Displays the location of an executable in your `PATH`.

Example:

```bash
which kubectl
```

Output:

```
/usr/bin/kubectl
```

Useful to confirm which binary is being executed.

---

# 10.4 whereis Command

Shows the binary, source, and manual page (if available).

Example:

```bash
whereis docker
```

Output:

```
docker: /usr/bin/docker /usr/share/man/man1/docker.1.gz
```

---

# 10.5 file Command

Determines the type of a file.

Example:

```bash
file notes.txt
```

Output:

```
notes.txt: ASCII text
```

Another example:

```bash
file nginx
```

Output:

```
ELF 64-bit executable
```

This is useful because Linux identifies files by their content, not just by file extension.

---

# 10.6 stat Command

Displays detailed metadata about a file.

Example:

```bash
stat notes.txt
```

Example output:

```
Size: 2048
Access: 2026-07-18
Modify: 2026-07-18
Change: 2026-07-18
Inode: 123456
Permissions: -rw-r--r--
```

`stat` is commonly used for troubleshooting timestamps, permissions, and ownership.

---

# 10.7 tree Command

Displays the directory structure in a tree format.

Example:

```bash
tree
```

Output:

```
Project/

├── Docker

│   ├── Dockerfile

│   └── compose.yaml

├── Kubernetes

│   ├── deployment.yaml

│   └── service.yaml

└── README.md
```

If `tree` isn't installed:

Ubuntu/Debian:

```bash
sudo apt install tree
```

RHEL/CentOS:

```bash
sudo yum install tree
```

---

# 11. Hidden Files

Linux hides files beginning with a dot (`.`).

Examples:

```
.bashrc

.profile

.gitconfig

.ssh/

.env
```

List hidden files:

```bash
ls -la
```

These files usually contain configuration rather than user data.

---

# 12. Wildcards

Wildcards allow pattern matching.

### `*`

Matches zero or more characters.

```bash
ls *.txt
```

---

### `?`

Matches exactly one character.

```bash
ls file?.txt
```

Matches:

```
file1.txt

file2.txt
```

Does not match:

```
file10.txt
```

---

### Character Ranges

```bash
ls file[1-5].txt
```

Matches:

```
file1.txt

file2.txt

file3.txt

file4.txt

file5.txt
```

---

# 13. Behind the Scenes: How Linux Stores Files

Linux stores files using **inodes**.

An inode contains metadata such as:

- File size
- Owner
- Permissions
- Timestamps
- Block locations

The filename is stored separately in the directory entry.

```
Filename

↓

Directory Entry

↓

Inode

↓

Disk Blocks
```

This design enables efficient file management and supports features like hard links.

---

# 14. DevOps Connection

Understanding files and directories is essential for daily DevOps work.

## Docker

Common locations:

```
/var/lib/docker
```

Stores images, containers, layers, and volumes.

---

## Kubernetes

```
/etc/kubernetes/
```

Configuration files.

```
/var/lib/kubelet/
```

Pod data and kubelet state.

---

## Nginx

```
/etc/nginx/
```

Configuration files.

```
/var/log/nginx/
```

Logs.

---

## SSH

```
/etc/ssh/
```

Server configuration.

```
~/.ssh/
```

User SSH keys.

---

## Logs

Most Linux logs are stored under:

```
/var/log
```

Examples:

```
syslog

messages

auth.log

kern.log
```

---

# 15. Production Scenario

A user reports that an application isn't starting.

Steps:

```bash
pwd
```

Confirm your location.

```bash
ls -la
```

Inspect files.

```bash
find . -name "*.yaml"
```

Locate Kubernetes manifests.

```bash
stat deployment.yaml
```

Verify timestamps.

```bash
file deployment.yaml
```

Confirm it's a text file.

```bash
cat deployment.yaml
```

Inspect the configuration.

This is a common troubleshooting workflow.

---

# 16. Common Mistakes

❌ Running `rm -rf` in the wrong directory.

❌ Forgetting to check the current directory with `pwd`.

❌ Confusing absolute and relative paths.

❌ Assuming file extensions determine file types.

❌ Using `find` from `/` unnecessarily, making searches slow.

---

# 17. Best Practices

- Use meaningful directory names.
- Prefer absolute paths in scripts.
- Verify before deleting.
- Organize projects logically.
- Use `find` instead of manually searching.
- Use `tree` to understand project structures.
- Keep configuration files under version control (e.g., Git).

---

# 18. Hands-on Lab

Create the following structure:

```
DevOps-Lab/

├── Docker/

├── Kubernetes/

├── Terraform/

├── Logs/

└── Scripts/
```

Practice:

```bash
mkdir -p DevOps-Lab/{Docker,Kubernetes,Terraform,Logs,Scripts}

cd DevOps-Lab

touch README.md

touch Docker/Dockerfile

touch Kubernetes/deployment.yaml

touch Kubernetes/service.yaml

cp README.md Logs/

mv README.md Scripts/

find . -name "*.yaml"

tree
```

---

# 19. Interview Questions

## Beginner

1. What is the difference between a file and a directory?
2. What does `pwd` do?
3. Explain `ls -la`.
4. What is a hidden file?
5. Difference between `cp` and `mv`.

## Intermediate

1. Explain absolute vs relative paths.
2. Difference between `find` and `locate`.
3. Why does `mv` within the same filesystem execute quickly?
4. What information does `stat` provide?
5. Explain inodes.

## Advanced

1. How does Linux map filenames to disk blocks?
2. Why can deleting a filename leave data accessible through another hard link?
3. What happens internally when `rm` is executed?
4. How would you locate recently modified configuration files on a production server?
5. Why is `rm -rf /` dangerous?

---

# 20. Quick Cheat Sheet

| Command | Purpose |
|----------|---------|
| `pwd` | Show current directory |
| `ls` | List files |
| `ls -la` | Show hidden files |
| `cd` | Change directory |
| `touch` | Create empty file |
| `mkdir` | Create directory |
| `mkdir -p` | Create nested directories |
| `cp` | Copy files |
| `mv` | Move or rename files |
| `rm` | Delete files |
| `rm -r` | Delete directories recursively |
| `rmdir` | Delete empty directory |
| `find` | Search files |
| `locate` | Fast file search |
| `which` | Find executable path |
| `whereis` | Find binary, source, and man page |
| `file` | Determine file type |
| `stat` | Show file metadata |
| `tree` | Display directory tree |

---

# 📝 Chapter Summary

In this chapter, you learned how to:

- Navigate the Linux filesystem.
- Create and organize files and directories.
- Copy, move, rename, and remove files safely.
- Search for files using `find` and `locate`.
- Inspect file types and metadata with `file` and `stat`.
- Understand hidden files, wildcards, and inodes.
- Apply these concepts in Docker, Kubernetes, and production Linux systems.

These skills form the foundation for nearly every Linux administration and DevOps task.
