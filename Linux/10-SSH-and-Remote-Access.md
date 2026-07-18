# ☸ Chapter 10 - SSH and Remote Access

> **"SSH is the standard way to securely access and manage remote Linux servers."**

---

# 📖 Table of Contents

1. What is Remote Access?
2. What is SSH?
3. Why SSH?
4. Telnet vs SSH
5. How SSH Works
6. SSH Architecture
7. Installing OpenSSH
8. SSH Client & Server
9. Connecting to a Remote Server
10. Authentication Methods
11. SSH Keys
12. Generating SSH Keys
13. Copying Public Keys
14. SSH Configuration
15. Common SSH Commands
16. SCP (Secure Copy)
17. SFTP
18. Port Forwarding
19. SSH Agent
20. SSH Config File
21. Troubleshooting SSH
22. Security Best Practices
23. Hands-on Lab
24. Interview Questions
25. Cheat Sheet
26. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you'll be able to:

- Understand SSH architecture
- Connect to remote Linux servers
- Configure SSH authentication
- Use passwordless login
- Transfer files securely
- Forward ports through SSH
- Harden SSH for production
- Troubleshoot common SSH issues

---

# 1. What is Remote Access?

Remote access means controlling another computer over a network without physically being in front of it.

Example:

```text
Your Laptop (Hyderabad)
        │
        │ Internet
        ▼
Linux Server (AWS Mumbai)
```

You can:

- Execute commands
- Edit files
- Restart services
- Deploy applications
- Monitor logs

---

# 2. What is SSH?

**SSH (Secure Shell)** is a secure network protocol used to access remote systems over an encrypted connection.

Default port:

```text
22
```

SSH provides:

- Secure login
- Remote command execution
- Secure file transfer
- Port forwarding
- Tunneling

---

# 3. Why SSH?

Without SSH, administrators often had to use insecure protocols or physically access servers.

SSH solves this by providing:

- Encryption
- Authentication
- Integrity
- Confidentiality

---

# 4. Telnet vs SSH

| Feature | Telnet | SSH |
|----------|--------|-----|
| Encryption | ❌ No | ✅ Yes |
| Default Port | 23 | 22 |
| Password Protection | Plain Text | Encrypted |
| Recommended Today | ❌ No | ✅ Yes |

Never use Telnet on production systems.

---

# 5. How SSH Works

```text
Client

↓

SSH Request

↓

Server

↓

Authentication

↓

Encrypted Session

↓

Remote Shell
```

Every command sent between the client and server is encrypted.

---

# 6. SSH Architecture

```text
+--------------------+
| SSH Client         |
+----------+---------+
           |
      Encrypted Connection
           |
+----------v---------+
| SSH Server         |
| sshd               |
+----------+---------+
           |
           v
     Linux Operating System
```

Components:

- SSH Client
- SSH Server (`sshd`)
- TCP Port 22
- Authentication
- Encryption

---

# 7. Installing OpenSSH

### Ubuntu / Debian

```bash
sudo apt update
sudo apt install openssh-server
```

### RHEL / CentOS

```bash
sudo yum install openssh-server
```

Check version:

```bash
ssh -V
```

---

# 8. SSH Client & Server

### SSH Client

Starts the connection.

Command:

```bash
ssh
```

### SSH Server

Listens for incoming SSH requests.

Service:

```text
sshd
```

Check service:

```bash
systemctl status ssh
```

or

```bash
systemctl status sshd
```

---

# 9. Connecting to a Remote Server

Syntax:

```bash
ssh username@server_ip
```

Example:

```bash
ssh ubuntu@192.168.1.10
```

Custom port:

```bash
ssh -p 2222 ubuntu@192.168.1.10
```

---

# 10. Authentication Methods

## Password Authentication

```text
Client
   │
Password
   │
Server verifies
```

Simple but less secure.

---

## Public Key Authentication

```text
Private Key
     │
     ▼
Client --------> Server
             Public Key
```

Recommended for production.

---

# 11. SSH Keys

SSH uses a key pair.

```text
Private Key

+

Public Key
```

Private Key

- Secret
- Never shared

Public Key

- Stored on server
- Safe to share

---

# 12. Generating SSH Keys

Recommended:

```bash
ssh-keygen -t ed25519
```

RSA:

```bash
ssh-keygen -t rsa -b 4096
```

Default location:

```text
~/.ssh/
```

Files:

```text
id_ed25519
id_ed25519.pub
```

---

# 13. Copying Public Keys

Automatic:

```bash
ssh-copy-id username@server_ip
```

Manual:

Append your public key to:

```text
~/.ssh/authorized_keys
```

---

# 14. SSH Configuration

Configuration file:

```text
/etc/ssh/sshd_config
```

Example:

```text
Port 22
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

---

# 15. Common SSH Commands

Connect:

```bash
ssh user@host
```

Verbose:

```bash
ssh -v user@host
```

Specific key:

```bash
ssh -i ~/.ssh/id_ed25519 user@host
```

Remote command:

```bash
ssh user@host "uptime"
```

---

# 16. SCP (Secure Copy)

Copy to remote:

```bash
scp file.txt user@host:/home/user/
```

Copy from remote:

```bash
scp user@host:/home/user/file.txt .
```

Copy folder:

```bash
scp -r project/ user@host:/home/user/
```

---

# 17. SFTP

Start:

```bash
sftp user@host
```

Commands:

```bash
ls
pwd
cd
put file.txt
get file.txt
exit
```

---

# 18. SSH Port Forwarding

Local forwarding:

```bash
ssh -L 8080:localhost:80 user@host
```

```text
Local Port 8080
       │
       ▼
Remote Port 80
```

Useful for accessing remote web applications securely.

---

# 19. SSH Agent

Start:

```bash
eval "$(ssh-agent -s)"
```

Add key:

```bash
ssh-add ~/.ssh/id_ed25519
```

---

# 20. SSH Config File

File:

```text
~/.ssh/config
```

Example:

```text
Host myserver
    HostName 192.168.1.10
    User ubuntu
    Port 22
    IdentityFile ~/.ssh/id_ed25519
```

Connect:

```bash
ssh myserver
```

---

# 21. Troubleshooting SSH

## Connection Refused

Possible reasons:

- SSH service not running
- Wrong port
- Firewall blocking

---

## Permission Denied

Check:

- Username
- Password
- SSH key
- File permissions

---

## Host Key Changed

```text
REMOTE HOST IDENTIFICATION HAS CHANGED!
```

Remove old key:

```bash
ssh-keygen -R server_ip
```

Reconnect.

---

# 22. Security Best Practices

- Disable root login
- Use SSH keys
- Disable password authentication
- Update OpenSSH regularly
- Use strong passphrases
- Restrict access with firewall
- Monitor SSH logs
- Avoid sharing private keys

---

# 23. Hands-on Lab

- Install OpenSSH
- Verify SSH service
- Generate SSH key pair
- Configure passwordless login
- Connect to remote server
- Transfer files with SCP
- Use SFTP
- Configure `~/.ssh/config`

---

# 24. Interview Questions

## Beginner

- What is SSH?
- What port does SSH use?
- SSH vs Telnet?
- What is sshd?

## Intermediate

- Explain SSH key authentication.
- SCP vs SFTP?
- What is `authorized_keys`?
- What is `known_hosts`?

## Advanced

- How does SSH establish a secure connection?
- Explain SSH port forwarding.
- How do you secure an SSH server?
- How do you fix "Permission denied (publickey)"?

---

# 25. Cheat Sheet

```bash
# Connect
ssh user@host

# Custom Port
ssh -p 2222 user@host

# Generate SSH Key
ssh-keygen -t ed25519

# Copy Public Key
ssh-copy-id user@host

# Copy File
scp file.txt user@host:/path

# Copy Directory
scp -r folder user@host:/path

# Start SFTP
sftp user@host

# Run Remote Command
ssh user@host "hostname"

# Verbose Mode
ssh -v user@host

# Remove Old Host Key
ssh-keygen -R host
```

---

# 26. Chapter Summary

In this chapter, you learned:

- Remote access fundamentals
- SSH architecture
- SSH client and server
- Password vs key authentication
- SSH configuration
- SCP and SFTP
- Port forwarding
- SSH agent
- SSH config file
- Troubleshooting
- Security best practices
