# 🔒 Chapter 14 - Linux Security

> **"Security is not a product—it's a continuous process. A secure Linux system protects data, services, and users from unauthorized access and attacks."**

---

# 📖 Table of Contents

1. Introduction to Linux Security
2. Security Principles
3. Authentication vs Authorization
4. Linux Users and Security
5. Password Management
6. Password Policies
7. Pluggable Authentication Modules (PAM)
8. The `sudo` Command
9. Configuring `sudo`
10. Understanding `sudoers`
11. SSH Security
12. Secure Remote Access
13. Security Best Practices
14. Hands-on Labs
15. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you'll be able to:

- Understand Linux security fundamentals
- Differentiate authentication and authorization
- Configure password policies
- Secure user accounts
- Manage privileged access using `sudo`
- Harden SSH access
- Follow Linux security best practices

---

# 1. Introduction to Linux Security

Linux is considered one of the most secure operating systems, but security is not enabled automatically. A system is only as secure as its configuration.

Security involves protecting:

- Users
- Files
- Services
- Applications
- Networks
- Hardware

The main goals of Linux security are:

- Prevent unauthorized access
- Protect sensitive data
- Ensure service availability
- Detect suspicious activity

---

# CIA Triad

The CIA Triad is the foundation of information security.

```text
          CIA TRIAD

        +---------------+
        | Confidentiality |
        +---------------+
                ▲
                │
                ▼
+---------------------------+
| Integrity     Availability |
+---------------------------+
```

### Confidentiality

Ensures that only authorized users can access data.

Example:

- File permissions
- Encryption
- SSH authentication

---

### Integrity

Ensures data is not modified without authorization.

Examples:

- Checksums
- Digital signatures
- File integrity monitoring

---

### Availability

Ensures systems remain accessible when needed.

Examples:

- RAID
- Backups
- Redundant servers
- High availability clusters

---

# 2. Security Principles

Linux security follows several important principles.

## Least Privilege

Users should only have the permissions required to perform their tasks.

Example:

```text
Developer
    │
    ├── Read project files ✅
    ├── Edit project files ✅
    └── Delete system files ❌
```

---

## Defense in Depth

Instead of relying on a single security layer, multiple layers of protection are used.

Example:

```text
Internet
    │
Firewall
    │
SSH Security
    │
User Authentication
    │
File Permissions
    │
Application Security
```

If one layer fails, the remaining layers continue to protect the system.

---

## Principle of Minimal Exposure

Only expose services that are required.

Instead of running:

- FTP
- Telnet
- HTTP
- SSH
- Database

Run only the necessary services.

---

# 3. Authentication vs Authorization

Many beginners confuse these terms.

## Authentication

Authentication answers the question:

> **Who are you?**

Examples:

- Username & password
- SSH key
- Fingerprint
- Multi-factor authentication (MFA)

---

## Authorization

Authorization answers:

> **What are you allowed to do?**

Example:

```text
User logs in
        │
Authentication
        │
Success
        │
Authorization
        │
Can Read?
Can Write?
Can Execute?
```

---

# Authentication Flow

```text
User
  │
  ▼
Login Request
  │
  ▼
Authentication
  │
  ▼
Authorization
  │
  ▼
Access Granted
```

---

# 4. Linux Users and Security

Every process in Linux runs as a user.

Common users:

| User | Purpose |
|------|---------|
| root | Superuser |
| daemon | Background services |
| nobody | Restricted user |
| www-data | Web server (Debian/Ubuntu) |
| nginx | Nginx service |

---

## The Root User

The `root` user has unrestricted access.

Capabilities:

- Install software
- Delete any file
- Manage users
- Change system configuration
- Shutdown the system

Because of this power:

- Avoid logging in directly as `root`.
- Use `sudo` instead.

---

# 5. Password Management

Strong passwords are a critical security measure.

A strong password should:

- Be at least 12–16 characters long
- Include uppercase and lowercase letters
- Include numbers
- Include special characters
- Avoid dictionary words and personal information

Example:

```text
Weak:  password123

Strong:  L!nux@2026#Secure
```

---

# Password Storage

Linux does **not** store plain-text passwords.

Password hashes are stored in:

```text
/etc/shadow
```

Only the root user can read this file.

View permissions:

```bash
ls -l /etc/shadow
```

---

# 6. Password Policies

Password aging policies help enforce regular password changes.

View password policy:

```bash
chage -l username
```

Set maximum password age to 90 days:

```bash
sudo chage -M 90 username
```

Set minimum age:

```bash
sudo chage -m 7 username
```

Force password change on next login:

```bash
sudo chage -d 0 username
```

---

# 7. Pluggable Authentication Modules (PAM)

PAM (Pluggable Authentication Modules) provides a flexible framework for authentication.

Instead of every application implementing its own authentication mechanism, they use PAM.

Architecture:

```text
Application
      │
      ▼
     PAM
      │
      ▼
Authentication Modules
      │
      ▼
Password / LDAP / Kerberos
```

PAM configuration files are located in:

```text
/etc/pam.d/
```

---

# 8. The `sudo` Command

`sudo` allows authorized users to execute commands with elevated privileges.

Example:

```bash
sudo apt update
```

Instead of logging in as `root`, users temporarily gain administrative privileges.

Advantages:

- Improved security
- Better auditing
- Reduced risk of accidental system changes

---

# 9. Configuring `sudo`

Add a user to the sudo group (Ubuntu):

```bash
sudo usermod -aG sudo username
```

On RHEL-based systems:

```bash
sudo usermod -aG wheel username
```

Verify group membership:

```bash
groups username
```

---

# 10. Understanding the `sudoers` File

The `sudoers` file defines who can run privileged commands.

Location:

```text
/etc/sudoers
```

Always edit it using:

```bash
sudo visudo
```

This performs syntax checks before saving.

Example entry:

```text
aman ALL=(ALL:ALL) ALL
```

Meaning:

- User: `aman`
- Host: ALL
- Run as: ALL
- Commands: ALL

---

# 11. SSH Security

SSH (Secure Shell) is the standard protocol for secure remote access.

Default port:

```text
22
```

Check SSH service:

```bash
systemctl status ssh
```

---

## Disable Root Login

Edit:

```text
/etc/ssh/sshd_config
```

Set:

```text
PermitRootLogin no
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

---

## Disable Password Authentication

Use SSH keys instead of passwords.

In `sshd_config`:

```text
PasswordAuthentication no
```

This reduces the risk of brute-force attacks.

---

## Change the Default SSH Port

Example:

```text
Port 2222
```

> **Note:** Changing the default port reduces automated scanning but is **not** a replacement for proper security controls.

Restart the SSH service after making changes.

---

# 12. Secure Remote Access

Recommended practices:

- Use SSH keys instead of passwords
- Disable root login
- Disable unused accounts
- Enable a firewall
- Restrict SSH access to trusted IPs
- Keep OpenSSH updated

---

# 13. Security Best Practices

- Apply security updates regularly.
- Use strong passwords.
- Follow the principle of least privilege.
- Disable unnecessary services.
- Monitor authentication logs.
- Use SSH keys instead of passwords.
- Avoid logging in directly as `root`.
- Lock inactive accounts.
- Remove unused software.
- Back up important data.

---

# Hands-on Labs

## Lab 1: View Password Policy

```bash
chage -l $USER
```

---

## Lab 2: Check `/etc/shadow`

```bash
sudo ls -l /etc/shadow
```

---

## Lab 3: Verify Sudo Access

```bash
sudo whoami
```

Expected output:

```text
root
```

---

## Lab 4: View SSH Configuration

```bash
cat /etc/ssh/sshd_config
```

---

# Chapter Progress

✅ Linux Security Fundamentals

✅ CIA Triad

✅ Authentication

✅ Authorization

✅ Password Policies

✅ PAM

✅ sudo

✅ sudoers

✅ SSH Security

---

---

# 14. File Permissions Review

One of Linux's strongest security mechanisms is its file permission system.

Every file and directory has:

- Owner (User)
- Group
- Others

Each category has three basic permissions:

- Read (`r`)
- Write (`w`)
- Execute (`x`)

Example:

```text
-rwxr-x---
```

Breakdown:

```text
- rwx r-x ---
│ │   │   │
│ │   │   └── Others
│ │   └────── Group
│ └────────── Owner
└──────────── File Type
```

---

## Permission Values

| Permission | Binary | Numeric |
|------------|--------|---------|
| Read | 100 | 4 |
| Write | 010 | 2 |
| Execute | 001 | 1 |

Examples:

| Permission | Value |
|------------|-------|
| rwx | 7 |
| rw- | 6 |
| r-x | 5 |
| r-- | 4 |
| --- | 0 |

Example:

```bash
chmod 755 file.sh
```

Equivalent to:

```text
Owner : rwx
Group : r-x
Others: r-x
```

---

# Viewing Permissions

```bash
ls -l
```

Example:

```text
-rwxr-xr-- 1 aman developers 2048 Jul 20 app.sh
```

---

# Changing Permissions

Using symbolic mode:

```bash
chmod u+x file

chmod g-w file

chmod o-r file
```

Using numeric mode:

```bash
chmod 644 file.txt

chmod 755 script.sh

chmod 600 secret.txt
```

---

# Changing Ownership

Change owner:

```bash
sudo chown user file
```

Change owner and group:

```bash
sudo chown user:group file
```

Change only group:

```bash
sudo chgrp developers file
```

---

# 15. Special Permissions

Linux provides three special permission bits:

- SUID
- SGID
- Sticky Bit

---

## SUID (Set User ID)

When an executable has the SUID bit set, it runs with the permissions of the file owner.

Example:

```bash
chmod u+s program
```

View:

```text
-rwsr-xr-x
```

Common example:

```bash
/usr/bin/passwd
```

Users can change their passwords even though `/etc/shadow` is owned by root.

---

## SGID (Set Group ID)

Files:

```bash
chmod g+s file
```

Directories:

New files inherit the directory's group.

Example:

```text
drwxrwsr-x
```

Useful for shared project directories.

---

## Sticky Bit

Commonly used on shared directories like `/tmp`.

Set:

```bash
chmod +t /shared
```

Permissions:

```text
drwxrwxrwt
```

Only the file owner can delete their own files.

---

# 16. Access Control Lists (ACL)

Traditional Linux permissions provide only three levels:

- Owner
- Group
- Others

ACLs allow assigning permissions to individual users or groups.

---

## Install ACL Tools

Ubuntu:

```bash
sudo apt install acl
```

---

## View ACL

```bash
getfacl file.txt
```

---

## Add ACL

```bash
setfacl -m u:john:rwx file.txt
```

---

## Remove ACL

```bash
setfacl -x u:john file.txt
```

---

## Remove All ACLs

```bash
setfacl -b file.txt
```

---

# 17. Linux Firewalls

A firewall controls incoming and outgoing network traffic.

```text
Internet
     │
     ▼
+-------------+
|  Firewall   |
+-------------+
     │
     ▼
Linux Server
```

Firewall rules determine:

- Which ports are open
- Which IPs are allowed
- Which services are accessible

---

# 18. UFW (Uncomplicated Firewall)

Default firewall on Ubuntu.

Check status:

```bash
sudo ufw status
```

Enable:

```bash
sudo ufw enable
```

Disable:

```bash
sudo ufw disable
```

---

## Allow SSH

```bash
sudo ufw allow ssh
```

---

## Allow Port 80

```bash
sudo ufw allow 80
```

---

## Allow HTTPS

```bash
sudo ufw allow 443
```

---

## Delete Rule

```bash
sudo ufw delete allow 80
```

---

## Deny a Port

```bash
sudo ufw deny 23
```

---

# 19. firewalld

Common on RHEL, CentOS, Rocky Linux, and AlmaLinux.

Check status:

```bash
systemctl status firewalld
```

Enable:

```bash
sudo systemctl enable --now firewalld
```

---

## List Active Zones

```bash
firewall-cmd --get-active-zones
```

---

## Open HTTP

```bash
sudo firewall-cmd --add-service=http --permanent

sudo firewall-cmd --reload
```

---

## Open HTTPS

```bash
sudo firewall-cmd --add-service=https --permanent

sudo firewall-cmd --reload
```

---

## List Rules

```bash
firewall-cmd --list-all
```

---

# UFW vs firewalld

| Feature | UFW | firewalld |
|---------|-----|-----------|
| Ubuntu | ✅ | ❌ |
| RHEL | ❌ | ✅ |
| Easy to Use | ✅ | Moderate |
| Zones | ❌ | ✅ |
| Dynamic Rules | Limited | ✅ |

---

# 20. SELinux

SELinux stands for **Security-Enhanced Linux**.

It provides **Mandatory Access Control (MAC)**.

Even if a user has permission to access a file, SELinux can still deny access based on security policies.

---

# SELinux Modes

Check mode:

```bash
getenforce
```

Possible outputs:

```text
Enforcing

Permissive

Disabled
```

---

## Enforcing

Policies are enforced.

Access violations are blocked.

---

## Permissive

Violations are logged but not blocked.

Useful for troubleshooting.

---

## Disabled

SELinux is disabled.

Not recommended for production.

---

# Change Mode

Temporary:

```bash
sudo setenforce 0
```

Enable again:

```bash
sudo setenforce 1
```

Permanent configuration:

```text
/etc/selinux/config
```

---

# View Contexts

```bash
ls -Z
```

---

# Restore Contexts

```bash
restorecon -Rv /var/www/html
```

---

# 21. AppArmor

AppArmor is an alternative to SELinux.

Common on:

- Ubuntu
- Debian

Check status:

```bash
sudo aa-status
```

Profiles are stored in:

```text
/etc/apparmor.d/
```

---

# AppArmor Modes

- Enforce
- Complain
- Disabled

---

# 22. Security Monitoring

Useful commands:

```bash
last
```

Shows login history.

---

Failed logins:

```bash
lastb
```

---

Current users:

```bash
who
```

---

Authentication logs:

Ubuntu:

```bash
cat /var/log/auth.log
```

RHEL:

```bash
cat /var/log/secure
```

---

Kernel messages:

```bash
dmesg
```

---

Journal logs:

```bash
journalctl
```

---

# 23. Auditing Basics

Linux auditing records security-related events.

Audit daemon:

```bash
systemctl status auditd
```

Search logs:

```bash
ausearch
```

Audit reports:

```bash
aureport
```

---

# Hands-on Labs

## Lab 1

Check permissions:

```bash
ls -l
```

---

## Lab 2

Change permissions:

```bash
chmod 755 script.sh
```

---

## Lab 3

Enable UFW:

```bash
sudo ufw enable
```

---

## Lab 4

Check SELinux mode:

```bash
getenforce
```

---

## Lab 5

View AppArmor status:

```bash
sudo aa-status
```

---

# Chapter Progress

✅ File Permissions

✅ Ownership

✅ ACL

✅ UFW

✅ firewalld

✅ SELinux

✅ AppArmor

✅ Security Monitoring

✅ Auditing

---

# 24. Fail2Ban

Fail2Ban is an intrusion prevention tool that protects Linux servers from
brute-force attacks.

It continuously monitors log files and automatically blocks IP addresses that
generate too many failed login attempts.

Common services protected:

- SSH
- Apache
- Nginx
- FTP
- Mail Servers

---

# How Fail2Ban Works

```text
Attacker
    │
    ▼
Repeated Failed Login Attempts
    │
    ▼
Fail2Ban Reads Logs
    │
    ▼
Threshold Reached
    │
    ▼
Firewall Rule Added
    │
    ▼
IP Address Blocked
```

---

## Install Fail2Ban

Ubuntu

```bash
sudo apt update

sudo apt install fail2ban
```

RHEL

```bash
sudo dnf install fail2ban
```

---

## Start Service

```bash
sudo systemctl enable --now fail2ban
```

Check status:

```bash
systemctl status fail2ban
```

---

## Configuration

Never edit:

```text
/etc/fail2ban/jail.conf
```

Instead copy:

```bash
sudo cp /etc/fail2ban/jail.conf \
/etc/fail2ban/jail.local
```

Edit:

```bash
sudo nano /etc/fail2ban/jail.local
```

---

## Important Parameters

```text
bantime = 10m

findtime = 10m

maxretry = 5
```

Meaning:

- Ban for 10 minutes
- Count failures during 10 minutes
- Ban after 5 failed attempts

---

## Check Active Jails

```bash
sudo fail2ban-client status
```

Example:

```text
Status

|- Number of jail: 1

`- sshd
```

---

## Check SSH Jail

```bash
sudo fail2ban-client status sshd
```

---

## Unban an IP

```bash
sudo fail2ban-client set sshd unbanip 192.168.1.10
```

---

# 25. SSH Hardening

SSH is usually the first service attackers target.

Always harden SSH before exposing a server to the Internet.

Configuration file:

```text
/etc/ssh/sshd_config
```

---

## Disable Root Login

```text
PermitRootLogin no
```

---

## Disable Empty Passwords

```text
PermitEmptyPasswords no
```

---

## Disable Password Authentication

```text
PasswordAuthentication no
```

Use SSH keys instead.

---

## Allow Specific Users

```text
AllowUsers aman ubuntu admin
```

---

## Limit Login Attempts

```text
MaxAuthTries 3
```

---

## Disconnect Idle Sessions

```text
ClientAliveInterval 300

ClientAliveCountMax 2
```

---

## Change Default Port

```text
Port 2222
```

Remember to update the firewall.

---

## Restart SSH

```bash
sudo systemctl restart ssh
```

Always verify configuration first:

```bash
sudo sshd -t
```

---

# 26. SSH Key Authentication

Generate key pair:

```bash
ssh-keygen -t ed25519
```

Copy key:

```bash
ssh-copy-id user@server
```

Connect:

```bash
ssh user@server
```

Benefits:

- Stronger authentication
- No password transmission
- Resistant to brute-force attacks

---

# 27. File Integrity Monitoring

Attackers often modify system files after gaining access.

File Integrity Monitoring detects unauthorized changes.

Popular tools:

- AIDE
- Tripwire
- Samhain

---

# AIDE (Advanced Intrusion Detection Environment)

Install:

Ubuntu

```bash
sudo apt install aide
```

Initialize database:

```bash
sudo aideinit
```

Run integrity check:

```bash
sudo aide --check
```

---

# How AIDE Works

```text
Initial Scan
      │
      ▼
Database Created
      │
      ▼
System Changes
      │
      ▼
AIDE Compares Files
      │
      ▼
Report Generated
```

---

# 28. Security Updates

Keeping systems updated is one of the simplest and most effective security measures.

Update package list:

Ubuntu

```bash
sudo apt update
```

Upgrade packages:

```bash
sudo apt upgrade
```

Full upgrade:

```bash
sudo apt full-upgrade
```

---

RHEL

```bash
sudo dnf update
```

---

# Automatic Security Updates

Ubuntu

```bash
sudo apt install unattended-upgrades
```

Enable:

```bash
sudo dpkg-reconfigure unattended-upgrades
```

---

# 29. Security Scanning

Regularly check your system for vulnerabilities.

Useful tools:

- Lynis
- chkrootkit
- rkhunter
- ClamAV

---

## Lynis

Install:

```bash
sudo apt install lynis
```

Run:

```bash
sudo lynis audit system
```

Reports include:

- Security score
- Missing patches
- Weak configurations
- Recommendations

---

## chkrootkit

Detect rootkits.

```bash
sudo apt install chkrootkit

sudo chkrootkit
```

---

## rkhunter

Install:

```bash
sudo apt install rkhunter
```

Update database:

```bash
sudo rkhunter --update
```

Run scan:

```bash
sudo rkhunter --check
```

---

# 30. Malware Detection

Although Linux malware is less common than Windows malware, servers can still be infected.

Install ClamAV:

```bash
sudo apt install clamav
```

Update signatures:

```bash
sudo freshclam
```

Scan home directory:

```bash
clamscan -r /home
```

---

# 31. Production Hardening Checklist

✔ Keep the operating system updated

✔ Disable unused services

✔ Remove unused packages

✔ Use SSH keys

✔ Disable root login

✔ Enable firewall

✔ Install Fail2Ban

✔ Monitor authentication logs

✔ Restrict sudo access

✔ Enable automatic updates

✔ Configure backups

✔ Monitor disk usage

✔ Rotate logs

✔ Enable auditing

✔ Use strong passwords

✔ Enable MFA where possible

---

# 32. Real-World Security Scenarios

## Scenario 1

### SSH Brute Force Attack

Symptoms:

- Thousands of failed SSH login attempts
- High CPU usage
- Authentication log grows rapidly

Investigation:

```bash
journalctl -u ssh

lastb
```

Solution:

- Install Fail2Ban
- Disable password authentication
- Use SSH keys

---

## Scenario 2

### Unexpected Root Login

Check:

```bash
last

lastlog
```

Review:

```bash
cat /var/log/auth.log
```

Investigate:

- Source IP
- Time
- Commands executed

---

## Scenario 3

### Suspicious Process

Check:

```bash
ps aux
```

Investigate:

```bash
lsof -p PID
```

Network connections:

```bash
ss -tulpn
```

Kill if malicious:

```bash
kill -9 PID
```

---

## Scenario 4

### Open Unexpected Port

Check:

```bash
ss -tulpn
```

or

```bash
netstat -tulpn
```

Stop unnecessary service.

Block the port using the firewall.

---

## Scenario 5

### Files Modified Unexpectedly

Run:

```bash
sudo aide --check
```

Review logs:

```bash
journalctl
```

Restore files from backup if required.

---

# Hands-on Labs

## Lab 1

Install Fail2Ban.

Check active jails.

---

## Lab 2

Generate SSH keys.

Login using SSH keys.

Disable password authentication.

---

## Lab 3

Install AIDE.

Initialize the database.

Run an integrity check.

---

## Lab 4

Run Lynis.

Review security recommendations.

---

## Lab 5

Run ClamAV scan.

---

# Chapter Progress

✅ Fail2Ban

✅ SSH Hardening

✅ SSH Keys

✅ File Integrity Monitoring

✅ Security Updates

✅ Security Scanning

✅ Malware Detection

✅ Production Hardening

✅ Real-world Scenarios

---
---

# 33. Incident Response

Despite strong security measures, security incidents can still occur. A well-defined incident response process helps minimize damage and restore services quickly.

The standard incident response lifecycle consists of:

1. Preparation
2. Identification
3. Containment
4. Eradication
5. Recovery
6. Lessons Learned

---

## Incident Response Workflow

```text
Security Alert
      │
      ▼
Identify Incident
      │
      ▼
Contain the Threat
      │
      ▼
Remove Malicious Activity
      │
      ▼
Recover Services
      │
      ▼
Post-Incident Review
```

---

## Step 1: Preparation

Preparation reduces the impact of future attacks.

Checklist:

- Keep backups
- Document procedures
- Enable logging
- Configure monitoring
- Maintain an inventory of systems
- Train administrators

---

## Step 2: Identification

Determine:

- What happened?
- Which systems are affected?
- When did it begin?
- How severe is the incident?

Useful commands:

```bash
journalctl

last

lastb

who

w

ps aux

ss -tulpn
```

---

## Step 3: Containment

Goal:

Prevent the attack from spreading.

Examples:

- Disconnect compromised servers
- Block malicious IP addresses
- Disable compromised accounts
- Stop vulnerable services

---

## Step 4: Eradication

Remove the root cause.

Examples:

- Remove malware
- Patch vulnerabilities
- Delete malicious accounts
- Remove unauthorized SSH keys

---

## Step 5: Recovery

Restore normal operations.

- Restore data from backups
- Restart services
- Monitor systems closely
- Verify application functionality

---

## Step 6: Lessons Learned

After recovery:

- Document the incident
- Identify weaknesses
- Improve monitoring
- Update security policies

---

# 34. Security Auditing

Security auditing helps verify that systems comply with security policies.

Common audit areas:

- User accounts
- Password policies
- File permissions
- Running services
- Installed packages
- Firewall configuration
- Open ports
- Log review

---

## Check Running Services

```bash
systemctl list-units --type=service
```

---

## Check Open Ports

```bash
ss -tulpn
```

---

## Review Login History

```bash
last
```

---

## Review Failed Login Attempts

```bash
lastb
```

---

## Check Sudo Activity

```bash
journalctl | grep sudo
```

---

## Review Installed Packages

Ubuntu:

```bash
apt list --installed
```

RHEL:

```bash
rpm -qa
```

---

# 35. Compliance Basics

Many organizations follow security standards and compliance frameworks.

Examples:

- ISO 27001
- CIS Benchmarks
- PCI DSS
- HIPAA
- SOC 2

Linux administrators often help meet compliance by:

- Applying security patches
- Enforcing password policies
- Protecting sensitive data
- Maintaining audit logs
- Restricting administrative access

---

# 36. Security Checklist

## User Security

- Remove unused users
- Disable inactive accounts
- Enforce password policies
- Use MFA where available

---

## SSH Security

- Disable root login
- Disable password authentication
- Use SSH keys
- Restrict allowed users
- Change the default port if appropriate

---

## Firewall

- Enable firewall
- Allow only required ports
- Remove unused rules

---

## Updates

- Install security patches regularly
- Enable automatic updates where appropriate

---

## File Security

- Use correct file permissions
- Review SUID/SGID files
- Protect sensitive configuration files

---

## Monitoring

- Monitor authentication logs
- Review system logs
- Rotate logs
- Monitor disk usage
- Monitor CPU and memory usage

---

## Backup

- Test backups regularly
- Store backups securely
- Verify restore procedures

---

# 37. Linux Security Command Cheat Sheet

## User Management

```bash
who

w

id

groups

passwd

chage -l username
```

---

## Permissions

```bash
ls -l

chmod

chown

chgrp

getfacl

setfacl
```

---

## SSH

```bash
systemctl status ssh

sshd -t

ssh-keygen

ssh-copy-id
```

---

## Firewall

### UFW

```bash
ufw status

ufw allow 22

ufw delete allow 22
```

### firewalld

```bash
firewall-cmd --list-all

firewall-cmd --reload
```

---

## SELinux

```bash
getenforce

setenforce 0

setenforce 1

restorecon -Rv
```

---

## AppArmor

```bash
aa-status
```

---

## Logs

```bash
journalctl

journalctl -u ssh

tail -f /var/log/auth.log
```

---

## Auditing

```bash
ausearch

aureport

last

lastb
```

---

## Fail2Ban

```bash
fail2ban-client status

fail2ban-client status sshd
```

---

# 38. Interview Questions

## Beginner

1. What is Linux security?
2. What is the CIA Triad?
3. Difference between authentication and authorization?
4. What is `sudo`?
5. Why should you avoid logging in directly as `root`?

---

## Intermediate

1. Explain PAM.
2. Difference between SELinux and AppArmor.
3. What is Fail2Ban?
4. Explain ACLs.
5. Difference between `chmod` and `chown`.
6. How does SSH key authentication work?

---

## Advanced

1. How would you secure a newly installed Linux server?
2. Explain Linux hardening.
3. How would you investigate unauthorized access?
4. How do you troubleshoot repeated SSH login failures?
5. Explain the difference between DAC and MAC.
6. What are the advantages of centralized logging?

---

# 39. Scenario-Based Interview Questions

### Scenario 1

A server is experiencing repeated SSH brute-force attacks.

How would you secure it?

Expected points:

- Use SSH keys
- Disable password authentication
- Install Fail2Ban
- Enable firewall
- Review logs

---

### Scenario 2

A developer accidentally received root privileges.

How would you fix it?

Expected answer:

- Remove unnecessary `sudo` access
- Review `/etc/sudoers`
- Verify group memberships
- Audit recent commands

---

### Scenario 3

Users report they cannot access a web application after enabling SELinux.

What would you check?

Expected answer:

- SELinux mode
- Security contexts (`ls -Z`)
- Audit logs
- Restore contexts (`restorecon`)

---

### Scenario 4

A suspicious process is consuming CPU.

Commands:

```bash
top

ps aux

lsof -p PID

ss -tulpn

journalctl
```

---

# 40. Chapter Summary

Congratulations! 🎉

You have completed the Linux Security chapter.

In this chapter, you learned:

- Security fundamentals
- CIA Triad
- Authentication vs Authorization
- Password policies
- PAM
- `sudo` and `sudoers`
- SSH security
- File permissions and ACLs
- UFW and firewalld
- SELinux
- AppArmor
- Fail2Ban
- File integrity monitoring
- Security updates
- Security scanning
- Malware detection
- Incident response
- Security auditing
- Compliance basics
- Production hardening
- Interview questions
- Security cheat sheet

---

# 41. Revision Notes

Remember these important configuration files:

```text
/etc/passwd

/etc/shadow

/etc/group

/etc/sudoers

/etc/ssh/sshd_config

/etc/pam.d/

/etc/fail2ban/jail.local

/etc/selinux/config

/etc/apparmor.d/
```

Important commands:

```text
chmod

chown

getfacl

setfacl

ufw

firewall-cmd

getenforce

restorecon

journalctl

last

lastb

ausearch

aureport

fail2ban-client
```

---

# 📚 Next Chapter

## File Name

```text
17-Linux-Troubleshooting.md
```

### Topics Covered

- Troubleshooting Methodology
- Boot Problems
- Service Failures
- CPU, Memory & Disk Issues
- Network Troubleshooting
- DNS Issues
- SSH Problems
- Package Management Issues
- Filesystem Errors
- Performance Analysis
- Real-World Scenarios
- Best Practices
- Hands-on Labs
- Interview Questions
- One-Page Cheat Sheet
