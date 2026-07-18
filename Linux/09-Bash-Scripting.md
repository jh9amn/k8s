# 🐚 Chapter 9 - Bash Scripting

> **"If you repeat a command more than twice, automate it."**

Bash scripting is one of the most valuable Linux skills. Instead of executing commands manually, you can write them once in a script and run them whenever needed.

Bash is used extensively in:

- Linux Administration
- DevOps
- Docker
- Kubernetes
- CI/CD Pipelines
- Cloud Automation
- System Monitoring

---

# 📖 Table of Contents

1. What is Bash?
2. Why Bash Scripting?
3. How Bash Executes Commands
4. Creating Your First Script
5. Running Scripts
6. Variables
7. Environment Variables
8. User Input
9. Comments
10. Quoting
11. Command Substitution
12. Exit Status
13. Basic Operators
14. Hands-on Practice

---

# 🎯 Learning Objectives

After this chapter you'll be able to:

- Write Bash scripts
- Use variables
- Read user input
- Understand exit codes
- Work with environment variables
- Automate repetitive tasks

---

# 1. What is Bash?

Bash stands for:

```
Bourne Again SHell
```

It is:

- A command-line shell
- A scripting language
- The default shell on most Linux systems

Examples of other shells:

- sh
- bash
- zsh
- fish
- ksh

---

# Shell vs Terminal

Many beginners confuse these terms.

### Terminal

The application you open.

Examples:

- GNOME Terminal
- Windows Terminal
- iTerm2

### Shell

The program running inside the terminal.

```
Terminal

↓

Bash
```

Check your shell:

```bash
echo $SHELL
```

---

# 2. Why Bash Scripting?

Suppose every morning you run:

```bash
git pull

docker compose up -d

kubectl get pods

kubectl get svc
```

Instead of typing these every day, create:

```bash
start-project.sh
```

Run:

```bash
./start-project.sh
```

Automation saves time and reduces errors.

---

# 3. How Bash Executes Commands

When you type:

```bash
ls
```

Bash:

```
User

↓

Bash

↓

Search PATH

↓

Execute Program

↓

Display Output
```

View PATH:

```bash
echo $PATH
```

---

# 4. Creating Your First Script

Create:

```bash
vim hello.sh
```

Content:

```bash
#!/bin/bash

echo "Hello Linux!"
```

Save.

Make executable:

```bash
chmod +x hello.sh
```

Run:

```bash
./hello.sh
```

Output:

```
Hello Linux!
```

---

# 5. Shebang (`#!`)

The first line:

```bash
#!/bin/bash
```

tells Linux which interpreter should execute the script.

Examples:

```bash
#!/bin/bash
```

```bash
#!/usr/bin/env bash
```

```bash
#!/usr/bin/python3
```

Without a proper shebang, the script may run with a different shell depending on how it is invoked.

---

# 6. Variables

Variables store values.

```bash
name="Aman"

echo "$name"
```

Output:

```
Aman
```

---

## Rules

✅ Valid:

```bash
age=22
city="Hyderabad"
```

❌ Invalid:

```bash
first name="Aman"
```

No spaces around `=`.

---

# 7. Environment Variables

Environment variables are inherited by child processes.

Common variables:

```bash
HOME
USER
PATH
PWD
SHELL
HOSTNAME
```

Examples:

```bash
echo $HOME
echo $USER
echo $PWD
echo $HOSTNAME
```

Create one:

```bash
export PROJECT=Kubernetes
```

Use:

```bash
echo $PROJECT
```

---

# 8. User Input

Read user input using `read`.

Example:

```bash
#!/bin/bash

echo "Enter your name:"

read name

echo "Welcome $name"
```

Run:

```
Enter your name:
Aman

Welcome Aman
```

---

## Read Silently (Password)

```bash
read -s password
```

The typed characters are not displayed.

---

# 9. Comments

Single-line comment:

```bash
# This is a comment
```

Use comments to explain logic, not obvious commands.

---

# 10. Quoting

## Double Quotes

Variables expand.

```bash
name="Linux"

echo "$name"
```

Output:

```
Linux
```

---

## Single Quotes

Variables are treated literally.

```bash
echo '$name'
```

Output:

```
$name
```

---

## When to Quote Variables

Prefer:

```bash
echo "$file"
```

instead of:

```bash
echo $file
```

Quoting prevents issues with spaces and special characters.

---

# 11. Command Substitution

Capture command output.

Modern syntax:

```bash
today=$(date)

echo "$today"
```

Older syntax:

```bash
today=`date`
```

The `$(...)` form is recommended because it is easier to read and nest.

---

# 12. Exit Status

Every command returns an exit status.

Check the last command's status:

```bash
echo $?
```

Common meanings:

| Code | Meaning |
|------|---------|
| 0 | Success |
| Non-zero | Failure |

Example:

```bash
mkdir test
echo $?
```

---

# 13. Basic Arithmetic

Use arithmetic expansion:

```bash
a=10
b=20

echo $((a+b))
```

Output:

```
30
```

Other examples:

```bash
echo $((a-b))
echo $((a*b))
echo $((b/a))
echo $((b%a))
```

---

# 14. Hands-on Lab

Create a script:

```bash
vim info.sh
```

Content:

```bash
#!/bin/bash

echo "User : $USER"
echo "Home : $HOME"
echo "Current Directory : $PWD"
echo "Date : $(date)"
echo "Hostname : $(hostname)"
```

Make executable:

```bash
chmod +x info.sh
```

Run:

```bash
./info.sh
```

---

# Learning Summary

In this part, you learned:

- What Bash is
- Shell vs Terminal
- Creating scripts
- Shebang
- Variables
- Environment variables
- User input
- Comments
- Quoting
- Command substitution
- Exit status
- Basic arithmetic

---

---

# 15. Conditional Statements (if)

The `if` statement allows your script to make decisions.

## Syntax

```bash
if condition
then
    commands
fi
```

Example:

```bash
#!/bin/bash

age=20

if [ "$age" -ge 18 ]
then
    echo "Eligible to vote"
fi
```

Output:

```
Eligible to vote
```

---

# 16. if...else

Execute different code depending on the condition.

```bash
#!/bin/bash

age=16

if [ "$age" -ge 18 ]
then
    echo "Adult"
else
    echo "Minor"
fi
```

Output:

```
Minor
```

---

# 17. if...elif...else

Useful when checking multiple conditions.

```bash
marks=75

if [ "$marks" -ge 90 ]
then
    echo "Grade A"

elif [ "$marks" -ge 75 ]
then
    echo "Grade B"

elif [ "$marks" -ge 60 ]
then
    echo "Grade C"

else
    echo "Fail"
fi
```

---

# 18. Test Operators

## Numeric Comparison

| Operator | Meaning |
|----------|---------|
| -eq | Equal |
| -ne | Not Equal |
| -gt | Greater Than |
| -lt | Less Than |
| -ge | Greater Than or Equal |
| -le | Less Than or Equal |

Example:

```bash
if [ "$a" -gt "$b" ]
then
    echo "a is greater"
fi
```

---

## String Comparison

| Operator | Meaning |
|----------|---------|
| = | Equal |
| != | Not Equal |
| -z | Empty String |
| -n | Not Empty |

Example:

```bash
name="Aman"

if [ "$name" = "Aman" ]
then
    echo "Welcome"
fi
```

---

## File Tests

| Test | Meaning |
|------|---------|
| -f | File exists |
| -d | Directory exists |
| -r | Readable |
| -w | Writable |
| -x | Executable |
| -e | Exists |

Example:

```bash
if [ -f "/etc/passwd" ]
then
    echo "File exists"
fi
```

---

# 19. Logical Operators

AND

```bash
if [ "$age" -gt 18 ] && [ "$age" -lt 60 ]
then
    echo "Working age"
fi
```

OR

```bash
if [ "$city" = "Delhi" ] || [ "$city" = "Mumbai" ]
then
    echo "Metro City"
fi
```

NOT

```bash
if [ ! -f "demo.txt" ]
then
    echo "File not found"
fi
```

---

# 20. case Statement

Cleaner than multiple `if...elif` blocks.

```bash
read -p "Enter option: " choice

case $choice in

1)
    echo "Start"
    ;;

2)
    echo "Stop"
    ;;

3)
    echo "Restart"
    ;;

*)
    echo "Invalid"
    ;;

esac
```

---

# 21. for Loop

Used when the number of iterations is known.

Example:

```bash
for i in 1 2 3 4 5
do
    echo $i
done
```

Output:

```
1
2
3
4
5
```

---

## Range

```bash
for i in {1..10}
do
    echo $i
done
```

---

## Iterate Through Files

```bash
for file in *.txt
do
    echo "$file"
done
```

---

# 22. while Loop

Runs while a condition is true.

```bash
count=1

while [ "$count" -le 5 ]
do
    echo "$count"
    count=$((count+1))
done
```

---

# 23. until Loop

Runs until the condition becomes true.

```bash
count=1

until [ "$count" -gt 5 ]
do
    echo "$count"
    count=$((count+1))
done
```

---

# 24. break

Stops the loop immediately.

```bash
for i in {1..10}
do
    if [ "$i" -eq 5 ]
    then
        break
    fi

    echo "$i"
done
```

Output:

```
1
2
3
4
```

---

# 25. continue

Skips the current iteration.

```bash
for i in {1..5}
do
    if [ "$i" -eq 3 ]
    then
        continue
    fi

    echo "$i"
done
```

Output:

```
1
2
4
5
```

---

# 26. Functions

Functions help avoid repeating code.

```bash
greet(){

    echo "Hello"

}

greet
```

---

## Function with Parameters

```bash
welcome(){

    echo "Welcome $1"

}

welcome Aman
```

Output:

```
Welcome Aman
```

---

# 27. Arrays

Create an array.

```bash
fruits=("Apple" "Banana" "Orange")
```

Access elements.

```bash
echo ${fruits[0]}
```

Output:

```
Apple
```

---

Print all elements.

```bash
echo ${fruits[@]}
```

Length.

```bash
echo ${#fruits[@]}
```

---

# 28. Positional Parameters

When running:

```bash
./hello.sh Aman Linux
```

Variables become:

```
$1 -> Aman

$2 -> Linux
```

Example:

```bash
echo "Name : $1"

echo "Course : $2"
```

---

# 29. Special Variables

| Variable | Meaning |
|----------|---------|
| $0 | Script Name |
| $1 | First Argument |
| $2 | Second Argument |
| $# | Number of Arguments |
| $@ | All Arguments |
| $$ | Process ID |
| $? | Exit Status |

Example:

```bash
echo $0
echo $#
echo $$
echo $?
```

---

# 30. Shift

`shift` removes the first positional parameter.

Example:

```
Before

$1 A

$2 B

$3 C
```

After:

```bash
shift
```

```
$1 B

$2 C
```

Useful for processing command-line arguments.

---

# 31. Reading Files Line by Line

```bash
while read line
do
    echo "$line"
done < file.txt
```

---

# 32. Hands-on Lab

## Even or Odd

```bash
read -p "Enter Number: " n

if (( n % 2 == 0 ))
then
    echo "Even"
else
    echo "Odd"
fi
```

---

## Print Numbers

```bash
for i in {1..20}
do
    echo $i
done
```

---

## Directory Check

```bash
read -p "Enter Directory: " dir

if [ -d "$dir" ]
then
    echo "Exists"
else
    echo "Not Found"
fi
```

---

# Learning Summary

You learned:

- if
- else
- elif
- case
- Comparison operators
- Logical operators
- for loop
- while loop
- until loop
- break
- continue
- Functions
- Arrays
- Command-line arguments
- Special variables
- Reading files

---

---

# 33. grep - Search Text

`grep` searches for text patterns inside files.

## Basic Syntax

```bash
grep "pattern" file.txt
```

Example:

```bash
grep "error" app.log
```

Shows all lines containing **error**.

---

## Ignore Case

```bash
grep -i "error" app.log
```

Matches:

```
Error
ERROR
error
```

---

## Count Matches

```bash
grep -c "ERROR" app.log
```

---

## Show Line Numbers

```bash
grep -n "ERROR" app.log
```

---

## Recursive Search

```bash
grep -r "TODO" .
```

Searches all files inside the current directory.

---

# 34. sed - Stream Editor

`sed` edits text without opening the file.

## Replace Text

```bash
sed 's/Linux/Ubuntu/' file.txt
```

Only replaces the **first occurrence** on each line.

---

## Replace All Occurrences

```bash
sed 's/Linux/Ubuntu/g' file.txt
```

---

## Edit File In Place

```bash
sed -i 's/http/https/g' config.txt
```

---

## Delete Line

```bash
sed '5d' file.txt
```

Deletes line 5.

---

# 35. awk

`awk` is used for processing structured text.

Example file:

```
Aman 95
Rahul 88
Priya 91
```

Print first column:

```bash
awk '{print $1}' marks.txt
```

Print second column:

```bash
awk '{print $2}' marks.txt
```

Filter rows:

```bash
awk '$2 > 90' marks.txt
```

---

# 36. cut

Extract specific columns.

Example:

```
Aman,DevOps,India
```

```bash
cut -d "," -f2 file.csv
```

Output:

```
DevOps
```

---

# 37. sort

Sort lines alphabetically.

```bash
sort file.txt
```

Reverse:

```bash
sort -r file.txt
```

Numeric:

```bash
sort -n numbers.txt
```

---

# 38. uniq

Remove duplicate lines.

```bash
uniq file.txt
```

Count duplicates:

```bash
uniq -c file.txt
```

Often combined with `sort`:

```bash
sort file.txt | uniq
```

---

# 39. find

Search files and directories.

Find all `.sh` files:

```bash
find . -name "*.sh"
```

Find directories:

```bash
find . -type d
```

Find files larger than 100 MB:

```bash
find . -size +100M
```

Delete log files:

```bash
find . -name "*.log" -delete
```

---

# 40. xargs

Converts input into command arguments.

Delete all `.log` files:

```bash
find . -name "*.log" | xargs rm
```

---

# 41. tee

Display output and save it to a file.

```bash
ls | tee output.txt
```

Append:

```bash
ls | tee -a output.txt
```

---

# 42. Regular Expressions

Common patterns:

| Pattern | Meaning |
|----------|---------|
| `.` | Any character |
| `*` | Zero or more |
| `+` | One or more |
| `^` | Start of line |
| `$` | End of line |
| `[0-9]` | Digit |
| `[a-z]` | Lowercase |
| `[A-Z]` | Uppercase |

Example:

```bash
grep "^ERROR" app.log
```

---

# 43. Error Handling

Exit immediately if a command fails:

```bash
set -e
```

Treat unset variables as errors:

```bash
set -u
```

Print executed commands:

```bash
set -x
```

Common production combination:

```bash
set -euo pipefail
```

This makes scripts more reliable.

---

# 44. Logging

Create logs with timestamps.

```bash
echo "$(date): Backup Started" >> backup.log
```

Redirect errors:

```bash
command 2> error.log
```

Redirect output:

```bash
command > output.log
```

Redirect both:

```bash
command > all.log 2>&1
```

---

# 45. Real-world Automation Scripts

## Disk Usage Alert

```bash
#!/bin/bash

usage=$(df / | awk 'NR==2 {print $5}' | tr -d '%')

if [ "$usage" -gt 80 ]
then
    echo "Disk usage exceeded 80%"
fi
```

---

## Backup Directory

```bash
#!/bin/bash

tar -czf backup.tar.gz /home/user/Documents
```

---

## Check Website Status

```bash
#!/bin/bash

curl -Is https://google.com | head -n 1
```

---

## Ping Multiple Servers

```bash
#!/bin/bash

for server in google.com github.com openai.com
do
    ping -c 2 "$server"
done
```

---

# 46. Cron Jobs

Cron runs scripts automatically.

Edit cron table:

```bash
crontab -e
```

View cron jobs:

```bash
crontab -l
```

Example:

Run every day at **2:30 AM**:

```cron
30 2 * * * /home/aman/backup.sh
```

Cron format:

```
Minute Hour Day Month Weekday
```

---

# 47. Bash Best Practices

✅ Use meaningful variable names.

```bash
disk_usage=75
```

---

✅ Quote variables.

```bash
echo "$file"
```

---

✅ Check exit status.

```bash
if command
then
    echo "Success"
fi
```

---

✅ Use functions for reusable code.

---

✅ Add comments for complex logic.

---

✅ Avoid running scripts as root unless necessary.

---

✅ Validate user input.

---

# 48. Common Mistakes

❌ Forgetting `chmod +x script.sh`

❌ Missing shebang (`#!/bin/bash`)

❌ Unquoted variables

```bash
rm $file
```

Correct:

```bash
rm "$file"
```

---

❌ Using spaces around `=`

Wrong:

```bash
name = Aman
```

Correct:

```bash
name="Aman"
```

---

❌ Ignoring exit codes.

Always check important commands.

---

# 49. Interview Questions

## Beginner

1. What is Bash?
2. Difference between Bash and Shell?
3. What is a shebang?
4. How do you declare variables?
5. What does `$?` represent?

---

## Intermediate

1. Difference between `$*` and `$@`?
2. Explain `grep`, `sed`, and `awk`.
3. Difference between `for` and `while`.
4. What is `set -e`?
5. How do cron jobs work?

---

## Advanced

1. Explain pipes.
2. What is process substitution?
3. Difference between `>` and `>>`?
4. Explain `exec`.
5. How do you debug Bash scripts?
6. Why use `set -euo pipefail`?
7. How would you write an idempotent Bash script?

---

# 50. One-Page Bash Cheat Sheet

| Command | Purpose |
|---------|---------|
| `chmod +x file.sh` | Make executable |
| `./file.sh` | Run script |
| `echo $VAR` | Print variable |
| `read name` | User input |
| `if ... fi` | Conditional |
| `case` | Multi-way branch |
| `for` | Loop |
| `while` | Loop |
| `break` | Exit loop |
| `continue` | Skip iteration |
| `function_name()` | Function |
| `grep` | Search text |
| `sed` | Replace/edit text |
| `awk` | Process columns |
| `cut` | Extract fields |
| `sort` | Sort lines |
| `uniq` | Remove duplicates |
| `find` | Search files |
| `xargs` | Build command arguments |
| `tee` | Display + save output |
| `crontab -e` | Edit cron jobs |

---

# 51. Practice Projects

1. System Information Script
2. Automatic Backup Script
3. Log File Analyzer
4. Disk Usage Monitor
5. CPU & Memory Monitor
6. Website Health Checker
7. Kubernetes Pod Status Checker
8. Docker Container Monitor
9. User Creation Script
10. File Organizer

These projects are excellent portfolio additions for DevOps interviews.

---

# 52. Chapter Summary

You now know how to:

- Write Bash scripts
- Use variables and environment variables
- Read user input
- Use conditions and loops
- Create functions
- Work with arrays
- Handle command-line arguments
- Process text with `grep`, `sed`, `awk`, `cut`, `sort`, and `uniq`
- Search files with `find`
- Automate tasks with cron
- Handle errors and logging
- Follow Bash best practices
- Build practical automation scripts
