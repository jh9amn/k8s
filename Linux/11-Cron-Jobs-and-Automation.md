# 🕒 Chapter 11 - Cron Jobs and Automation

> **"Automation is the key to efficient system administration. Cron allows Linux to execute tasks automatically at scheduled times."**

---

# 📖 Table of Contents

1. What is Automation?
2. What is Cron?
3. Cron Architecture
4. Cron Daemon (`cron` / `crond`)
5. Crontab
6. Crontab Syntax
7. Cron Time Fields
8. Special Cron Strings
9. Creating Cron Jobs
10. Managing Crontab
11. System-wide Cron
12. Cron Directories
13. Logging Cron Jobs
14. Redirecting Output
15. Environment Variables
16. Common Cron Examples
17. The `at` Command
18. Systemd Timers
19. Best Practices
20. Troubleshooting
21. Hands-on Lab
22. Interview Questions
23. Cheat Sheet
24. Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you'll be able to:

- Understand task automation in Linux
- Schedule jobs using cron
- Write cron expressions
- Manage user and system crontabs
- Redirect logs
- Use the `at` command
- Understand systemd timers
- Troubleshoot failed cron jobs

---

# 1. What is Automation?

Automation means executing tasks **without manual intervention**.

Examples:

- Daily database backup
- Log cleanup
- Sending reports
- Restarting services
- Disk usage monitoring
- Running scripts every hour

---

# 2. What is Cron?

Cron is a **time-based job scheduler** in Linux.

It automatically runs commands or scripts at scheduled times.

Examples:

- Every minute
- Every hour
- Every day
- Every Monday
- Every month
- Every year

---

# 3. Cron Architecture

```text
           Crontab
               │
               ▼
        Cron Daemon (crond)
               │
               ▼
     Executes Scheduled Jobs
               │
               ▼
      Scripts / Commands / Programs
```

---

# 4. Cron Daemon

The cron daemon runs in the background and continuously checks for scheduled jobs.

Service name:

```text
cron
```

or

```text
crond
```

Check status:

### Ubuntu/Debian

```bash
systemctl status cron
```

### RHEL/CentOS

```bash
systemctl status crond
```

Start service:

```bash
sudo systemctl start cron
```

Enable at boot:

```bash
sudo systemctl enable cron
```

---

# 5. Crontab

A **crontab** (cron table) stores scheduled jobs for a user.

View current crontab:

```bash
crontab -l
```

Edit crontab:

```bash
crontab -e
```

Remove crontab:

```bash
crontab -r
```

---

# 6. Crontab Syntax

General format:

```text
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of Week (0–7)
│ │ │ └──── Month (1–12)
│ │ └────── Day of Month (1–31)
│ └──────── Hour (0–23)
└────────── Minute (0–59)
```

---

# 7. Cron Time Fields

| Field | Range |
|--------|-------|
| Minute | 0–59 |
| Hour | 0–23 |
| Day of Month | 1–31 |
| Month | 1–12 |
| Day of Week | 0–7 (0 or 7 = Sunday) |

---

# 8. Special Cron Characters

| Symbol | Meaning |
|---------|---------|
| `*` | Every value |
| `,` | List |
| `-` | Range |
| `/` | Step values |

Examples:

Every minute:

```text
* * * * *
```

Every 10 minutes:

```text
*/10 * * * *
```

Every weekday:

```text
0 9 * * 1-5
```

At midnight every day:

```text
0 0 * * *
```

---

# 9. Special Cron Strings

Instead of writing five fields, you can use shortcuts.

| Shortcut | Meaning |
|----------|---------|
| `@reboot` | Run once after boot |
| `@yearly` | Once a year |
| `@monthly` | Once a month |
| `@weekly` | Once a week |
| `@daily` | Once a day |
| `@hourly` | Every hour |

Example:

```text
@daily /home/aman/backup.sh
```

---

# 10. Creating Cron Jobs

Example:

Run every day at 2:30 AM.

```text
30 2 * * * /home/aman/backup.sh
```

Run every Sunday at 5 PM.

```text
0 17 * * 0 /home/aman/report.sh
```

---

# 11. Managing Crontab

List:

```bash
crontab -l
```

Edit:

```bash
crontab -e
```

Delete:

```bash
crontab -r
```

Install from file:

```bash
crontab cronfile
```

---

# 12. System-wide Cron

System cron file:

```text
/etc/crontab
```

Additional cron directories:

```text
/etc/cron.daily
/etc/cron.hourly
/etc/cron.weekly
/etc/cron.monthly
```

Scripts placed in these directories run automatically.

---

# 13. Logging Cron Jobs

Redirect output:

```text
0 2 * * * /home/aman/backup.sh >> /var/log/backup.log 2>&1
```

Explanation:

- `>>` → Append output
- `2>&1` → Redirect errors to the same log

---

# 14. Redirecting Output

Discard output:

```text
> /dev/null 2>&1
```

Example:

```text
*/5 * * * * /home/aman/script.sh > /dev/null 2>&1
```

Useful for jobs that don't need logging.

---

# 15. Environment Variables

Cron runs with a minimal environment.

Specify variables explicitly.

Example:

```text
PATH=/usr/local/bin:/usr/bin:/bin
```

or use absolute paths in scripts.

---

# 16. Common Cron Examples

Run every minute:

```text
* * * * *
```

Every 15 minutes:

```text
*/15 * * * *
```

Every hour:

```text
0 * * * *
```

Every day at midnight:

```text
0 0 * * *
```

Every Monday at 8 AM:

```text
0 8 * * 1
```

Every January 1st:

```text
0 0 1 1 *
```

---

# 17. The `at` Command

Use `at` to schedule a **one-time** task.

Install:

```bash
sudo apt install at
```

Example:

```bash
at 10:30 PM
```

Then enter:

```bash
echo "Backup completed"
```

Finish with:

```text
Ctrl + D
```

List jobs:

```bash
atq
```

Delete a job:

```bash
atrm JOB_ID
```

---

# 18. Systemd Timers

Modern Linux systems can use **systemd timers** instead of cron.

Advantages:

- Better logging
- Dependency management
- Persistent timers
- More flexible scheduling

Useful commands:

```bash
systemctl list-timers
```

---

# 19. Best Practices

- Use absolute paths.
- Log important jobs.
- Test scripts manually before scheduling.
- Keep cron jobs simple.
- Store scripts in a dedicated directory.
- Add comments to complex schedules.
- Monitor disk space for log files.

---

# 20. Troubleshooting

## Cron job not running

Check:

- Cron service status
- Correct crontab syntax
- File permissions
- Script executable permission
- Correct PATH

Example:

```bash
chmod +x backup.sh
```

---

## Check Logs

Ubuntu:

```bash
grep CRON /var/log/syslog
```

RHEL:

```bash
grep CRON /var/log/cron
```

---

# 21. Hands-on Lab

1. Create a script:

```bash
nano hello.sh
```

```bash
#!/bin/bash
echo "Hello Linux"
```

2. Make executable:

```bash
chmod +x hello.sh
```

3. Schedule it every minute:

```text
* * * * * /home/user/hello.sh
```

4. Verify output in a log file.

---

# 22. Interview Questions

## Beginner

- What is cron?
- What is crontab?
- Difference between cron and at?

## Intermediate

- Explain cron syntax.
- What are special cron strings?
- Where are system cron jobs stored?

## Advanced

- Why do cron jobs sometimes fail?
- How do you debug cron jobs?
- Cron vs systemd timers?

---

# 23. Cheat Sheet

```bash
# Edit crontab
crontab -e

# List jobs
crontab -l

# Remove jobs
crontab -r

# Check cron service
systemctl status cron

# Start cron
sudo systemctl start cron

# Enable cron
sudo systemctl enable cron

# One-time job
at 10:30 PM

# List at jobs
atq

# Remove at job
atrm JOB_ID

# List timers
systemctl list-timers
```

Common schedules:

```text
* * * * *      Every minute
*/5 * * * *    Every 5 minutes
0 * * * *      Every hour
0 0 * * *      Daily at midnight
@daily         Once a day
@weekly        Once a week
@monthly       Once a month
@reboot        At startup
```

---

# 24. Chapter Summary

In this chapter, you learned:

- Linux task automation
- Cron daemon
- Crontab syntax
- Special cron expressions
- System-wide cron
- Logging and output redirection
- Environment variables
- `at` command
- systemd timers
- Troubleshooting
- Best practices
