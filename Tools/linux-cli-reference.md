# Linux Command Line Reference Guide
## For System Administrators

A comprehensive reference covering essential operations and powerful features that even experienced users might not know about.

---

## Table of Contents
1. [File & Directory Operations](#file--directory-operations)
2. [Text Processing & Search](#text-processing--search)
3. [Process & System Monitoring](#process--system-monitoring)
4. [User & Permission Management](#user--permission-management)
5. [Networking & Connectivity](#networking--connectivity)
6. [Disk & Storage Management](#disk--storage-management)
7. [Package Management](#package-management)
8. [Log Analysis](#log-analysis)
9. [Shell Scripting Essentials](#shell-scripting-essentials)
10. [Performance & Debugging](#performance--debugging)

---

## File & Directory Operations

### Basic Operations
```bash
# List files with human-readable sizes
ls -lh

# List all files including hidden, sorted by modification time
ls -laht

# Copy preserving all attributes
cp -a source dest

# Move multiple files
mv file1 file2 file3 /destination/

# Create nested directories
mkdir -p /path/to/nested/dirs

# Remove directory and contents
rm -rf directory/
```

### Advanced Operations
```bash
# Find files modified in last 7 days
find /path -type f -mtime -7

# Find files larger than 100MB
find /path -type f -size +100M

# Find and delete files older than 30 days
find /path -type f -mtime +30 -delete

# Copy only newer files (sync)
cp -u source/* dest/

# Create a backup with timestamp
cp file.txt{,."$(date +%Y%m%d_%H%M%S).bak"}
```

### 🎁 Easter Eggs & Power Tips

**1. Brace Expansion Magic**
```bash
# Create multiple directories at once
mkdir -p project/{src,bin,doc,test}

# Quick backup without typing filename twice
cp /etc/nginx/nginx.conf{,.backup}

# Rename with pattern
mv report.{txt,pdf}

# Create sequential files
touch file{1..10}.txt
```

**2. Advanced Find Techniques**
```bash
# Find and execute command on each file
find /var/log -name "*.log" -exec gzip {} \;

# Find with multiple conditions (AND)
find /home -type f -name "*.conf" -size +10k

# Find with OR conditions
find /var -type f \( -name "*.log" -o -name "*.tmp" \)

# Find files by inode (useful for files with weird names)
find . -inum 12345 -delete

# Find files excluding certain directories
find / -path /proc -prune -o -name "*.conf" -print
```

**3. Directory Navigation Shortcuts**
```bash
# Go to previous directory
cd -

# Create and enter directory in one command
mkdir -p /path/to/dir && cd $_

# Push/pop directory stack
pushd /var/log  # Save current dir and go to /var/log
popd            # Return to saved directory

# Show directory stack
dirs -v
```

---

## Text Processing & Search

### Basic Operations
```bash
# Search for pattern in files
grep "pattern" file.txt

# Search recursively in directory
grep -r "pattern" /path/

# Case-insensitive search
grep -i "pattern" file.txt

# Show line numbers
grep -n "pattern" file.txt
```

### Advanced Text Processing
```bash
# Search with context (3 lines before and after)
grep -C 3 "error" /var/log/syslog

# Multiple patterns (OR)
grep -E "error|warning|critical" logfile

# Inverse match (exclude pattern)
grep -v "debug" logfile

# Only show filenames with matches
grep -l "TODO" *.py
```

### sed - Stream Editor
```bash
# Replace first occurrence
sed 's/old/new/' file.txt

# Replace all occurrences
sed 's/old/new/g' file.txt

# Replace and save in-place
sed -i 's/old/new/g' file.txt

# Delete lines containing pattern
sed '/pattern/d' file.txt

# Print specific line range
sed -n '10,20p' file.txt
```

### awk - Pattern Scanning
```bash
# Print specific columns
awk '{print $1, $3}' file.txt

# Print lines longer than 80 characters
awk 'length > 80' file.txt

# Sum values in column
awk '{sum+=$1} END {print sum}' numbers.txt

# Print with custom field separator
awk -F: '{print $1, $3}' /etc/passwd
```

### 🎁 Easter Eggs & Power Tips

**1. Grep Power Moves**
```bash
# Recursive search with file type filtering
grep -r --include="*.py" "def " .

# Exclude directories from search
grep -r --exclude-dir={node_modules,.git} "pattern" .

# Perl-compatible regex for advanced patterns
grep -P '(?<=User: )\w+' logfile

# Count unique matches
grep -o "pattern" file | sort | uniq -c
```

**2. sed Advanced Techniques**
```bash
# Multiple substitutions
sed -e 's/foo/bar/g' -e 's/old/new/g' file.txt

# Backup before in-place edit
sed -i.bak 's/old/new/g' file.txt

# Delete empty lines
sed '/^$/d' file.txt

# Use different delimiter (useful when pattern contains /)
sed 's|/old/path|/new/path|g' file.txt

# Reference captured groups
sed 's/\([0-9]\+\)/Number: \1/g' file.txt
```

**3. awk Programming**
```bash
# BEGIN and END blocks
awk 'BEGIN {print "Report"} {sum+=$1} END {print "Total:", sum}' data.txt

# Multiple conditions
awk '$1 > 100 && $2 < 50 {print $0}' data.txt

# Count occurrences
awk '{count[$1]++} END {for (word in count) print word, count[word]}' file.txt

# Calculate average
awk '{sum+=$1; count++} END {print sum/count}' numbers.txt
```

---

## Process & System Monitoring

### Basic Operations
```bash
# List all processes
ps aux

# Process tree
pstree -p

# Real-time process monitoring
top
htop

# Kill process by PID
kill 1234

# Kill process by name
killall process_name

# Show system uptime
uptime

# Show memory usage
free -h
```

### 🎁 Easter Eggs & Power Tips

**1. Process Exploration**
```bash
# Show process environment variables
cat /proc/1234/environ | tr '\0' '\n'

# Show process working directory
readlink /proc/1234/cwd

# Find which process is using a file
fuser -v /path/to/file

# Show process tree with resource usage
ps aux --forest

# Sort processes by memory usage
ps aux --sort=-%mem | head

# Sort processes by CPU usage
ps aux --sort=-%cpu | head
```

**2. Top/Htop Shortcuts**
```bash
# Top shortcuts (while running):
# 1 - Show individual CPU cores
# M - Sort by memory usage
# P - Sort by CPU usage
# c - Show full command path
# k - Kill a process
# r - Renice a process
```

**3. Advanced Kill Techniques**
```bash
# Kill all processes matching pattern
pkill -f "pattern"

# Kill processes by user
pkill -u username

# Send custom signal
kill -HUP 1234   # Hangup (often causes config reload)

# Timeout command if runs too long
timeout 10s command  # Kill after 10 seconds
```

---

## User & Permission Management

### Basic Operations
```bash
# Add new user
useradd -m -s /bin/bash username

# Set/change password
passwd username

# Delete user
userdel -r username  # Also remove home directory

# Add user to group
usermod -aG groupname username

# Change file owner
chown user:group file

# Change permissions
chmod 755 file
chmod u+x file  # Add execute for user
```

### 🎁 Easter Eggs & Power Tips

**1. Permission Tricks**
```bash
# Find files with specific permissions
find /path -perm 777

# Find setuid files (security check)
find / -perm -4000 2>/dev/null

# Find world-writable files
find / -type f -perm -002 2>/dev/null

# Copy permissions from one file to another
chmod --reference=reference_file target_file

# Symbolic to numeric permission conversion
stat -c "%a %n" file

# Add execute permission only if already executable
chmod +X file  # Capital X
```

**2. Advanced ACL Management**
```bash
# View ACLs
getfacl file

# Set user ACL
setfacl -m u:john:rwx file

# Set group ACL
setfacl -m g:developers:rw file

# Set default ACLs for directory
setfacl -d -m u:john:rwx directory/

# Backup and restore ACLs
getfacl -R /path > acl_backup.txt
setfacl --restore=acl_backup.txt
```

**3. Sudo Power Moves**
```bash
# Edit file as root without opening whole shell
sudoedit /etc/config.conf

# Run previous command with sudo
sudo !!

# Sudo with piping
echo "content" | sudo tee /etc/file.conf

# Append with sudo
echo "content" | sudo tee -a /etc/file.conf
```

---

## Networking & Connectivity

### Basic Operations
```bash
# Show network interfaces
ip addr show

# Show routing table
ip route show

# Test connectivity
ping -c 4 host

# DNS lookup
dig domain.com
nslookup domain.com

# Show active connections
ss -tulpn

# Test port connectivity
nc -zv host port

# SSH connection
ssh user@host
```

### 🎁 Easter Eggs & Power Tips

**1. IP Command Mastery**
```bash
# Show brief interface status
ip -br addr show

# Colorized output
ip -c addr show

# Show interface statistics
ip -s link show eth0

# Monitor interface changes in real-time
ip monitor

# Show neighbor (ARP) table
ip neigh show
```

**2. Advanced SS (Socket Statistics)**
```bash
# Show all listening TCP sockets with process info
ss -tlp

# Show connections to specific port
ss -tn dst :443

# Show connections from specific IP
ss -tn src 192.168.1.100

# Count connections by state
ss -tan | awk '{print $1}' | sort | uniq -c
```

**3. Curl Power Features**
```bash
# Follow redirects
curl -L URL

# Send POST data
curl -X POST -d "param=value" URL

# Send JSON data
curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' URL

# Show response headers
curl -I URL

# Show request and response headers
curl -v URL

# Resume download
curl -C - -O URL
```

**4. SSH Power Moves**
```bash
# SSH tunnel (local port forwarding)
ssh -L 8080:localhost:80 user@host

# Remote port forwarding
ssh -R 8080:localhost:80 user@host

# Dynamic port forwarding (SOCKS proxy)
ssh -D 1080 user@host

# Keep connection alive
ssh -o ServerAliveInterval=60 user@host

# Copy SSH key to remote host
ssh-copy-id user@host

# Execute local script on remote host
ssh user@host 'bash -s' < local_script.sh
```

**5. DNS Investigation**
```bash
# Detailed DNS lookup
dig domain.com +trace
dig @8.8.8.8 domain.com

# Query specific record types
dig domain.com A
dig domain.com MX
dig domain.com TXT

# Short answer only
dig +short domain.com

# Reverse lookup
dig -x 8.8.8.8
```


---

## Disk & Storage Management

### Basic Operations
```bash
# Show disk usage by filesystem
df -h

# Show directory disk usage
du -sh directory

# Show largest directories
du -h | sort -rh | head -20

# List block devices
lsblk

# Mount filesystem
mount /dev/sdb1 /mnt

# Unmount
umount /mnt
```

### 🎁 Easter Eggs & Power Tips

**1. Disk Space Analysis**
```bash
# Find largest files in directory
find /path -type f -exec du -h {} + | sort -rh | head -20

# Find files by size range
find / -type f -size +50M -size -100M

# Find old large files
find /var/log -type f -size +10M -mtime +30

# Exclude directories from du
du -h --exclude='*.git' --exclude='node_modules' | sort -rh | head

# Find empty files
find /path -type f -empty

# Find empty directories
find /path -type d -empty
```

**2. Inodes Analysis**
```bash
# Show inode usage
df -i

# Find directories with most files
find / -xdev -type f | cut -d "/" -f 2 | sort | uniq -c | sort -rn | head

# Count files in directory
find /path -type f | wc -l
```

**3. Mount Mastery**
```bash
# Show all mount options
mount | grep "^/dev"

# Mount with specific options
mount -o ro,noexec /dev/sdb1 /mnt

# Remount with new options
mount -o remount,rw /dev/sdb1

# Bind mount (mirror directory)
mount --bind /source /destination

# Loop mount ISO
mount -o loop image.iso /mnt

# Show what process is using mount
lsof +D /mnt
fuser -mv /mnt

# Force unmount
umount -l /mnt  # Lazy unmount
```

**4. LVM (Logical Volume Manager)**
```bash
# Create physical volume
pvcreate /dev/sdb1

# Create volume group
vgcreate vg_name /dev/sdb1

# Create logical volume
lvcreate -L 10G -n lv_name vg_name

# Extend logical volume
lvextend -L +5G /dev/vg_name/lv_name
resize2fs /dev/vg_name/lv_name

# Show LVM information
pvdisplay
vgdisplay
lvdisplay

# Snapshot
lvcreate -L 5G -s -n snap_name /dev/vg_name/lv_name
```

---

## Package Management

### Debian/Ubuntu (apt)
```bash
# Update package list
apt update

# Upgrade all packages
apt upgrade

# Install package
apt install package_name

# Remove package
apt purge package_name  # Also remove config files

# Search for package
apt search keyword

# Show package info
apt show package_name

# Clean up
apt autoremove
```

### RHEL/CentOS (yum/dnf)
```bash
# Update and upgrade
dnf update

# Install package
dnf install package_name

# Remove package
dnf remove package_name

# Search for package
dnf search keyword

# Show package info
dnf info package_name

# Clean cache
dnf clean all
```

### 🎁 Easter Eggs & Power Tips

**1. APT Advanced**
```bash
# Download package without installing
apt download package_name

# Install local .deb file
apt install ./package.deb

# Simulate install (dry run)
apt install -s package_name

# Show reverse dependencies
apt-cache rdepends package_name

# List files installed by package
dpkg -L package_name

# Find which package provides file
dpkg -S /path/to/file

# Hold package at current version
apt-mark hold package_name

# List upgradeable packages
apt list --upgradeable

# Install specific version
apt install package_name=version

# Show changelog
apt changelog package_name
```

**2. YUM/DNF Advanced**
```bash
# List files in package
rpm -ql package_name

# Find package providing file
dnf provides /path/to/file

# Show package dependencies
dnf repoquery --requires package_name

# Show command history
dnf history

# Undo transaction
dnf history undo ID

# List enabled repositories
dnf repolist

# Install from specific repo
dnf --enablerepo=repo_name install package_name
```

**3. Package Investigation**
```bash
# Find recently installed packages (Debian/Ubuntu)
grep " install " /var/log/dpkg.log | tail -20

# Find recently installed (RHEL/CentOS)
rpm -qa --last | head -20

# Show package size
dpkg-query -W -f='${Installed-Size}\t${Package}\n' | sort -rn | head
rpm -qa --queryformat '%{SIZE} %{NAME}\n' | sort -rn | head

# Check package integrity
debsums -c
rpm -Va
```

---

## Log Analysis

### Basic Log Viewing
```bash
# View system log
journalctl

# Follow log in real-time
tail -f /var/log/syslog
journalctl -f

# View last N lines
tail -n 100 /var/log/syslog

# Search in logs
grep "error" /var/log/syslog

# Search with context
grep -C 5 "error" /var/log/syslog
```

### Journalctl Mastery
```bash
# Show logs since boot
journalctl -b

# Filter by time
journalctl --since "1 hour ago"
journalctl --since today

# Filter by service
journalctl -u nginx.service

# Filter by priority
journalctl -p err

# Follow logs
journalctl -f
```

### 🎁 Easter Eggs & Power Tips

**1. Journalctl Power Features**
```bash
# Show only today's logs
journalctl --since today

# Show logs between times
journalctl --since "2024-01-01" --until "2024-01-02"

# Show logs for specific PID
journalctl _PID=1234

# Show logs for specific user
journalctl _UID=1000

# Reverse order
journalctl -r

# Show disk usage
journalctl --disk-usage

# Vacuum old logs
journalctl --vacuum-time=7d
journalctl --vacuum-size=1G

# Show boot list
journalctl --list-boots

# Combine filters
journalctl -u nginx -p err --since "1 hour ago" -f
```

**2. Advanced Log Search**
```bash
# Search across rotated logs
zgrep "error" /var/log/syslog*

# Search with date range
awk '/Jan 15/,/Jan 16/' /var/log/syslog

# Search for IP addresses
grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' /var/log/auth.log

# Count unique IPs
grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' /var/log/auth.log | sort | uniq -c | sort -rn

# Failed SSH attempts
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn

# Find most common log entries
awk '{$1=$2=$3=""; print $0}' /var/log/syslog | sort | uniq -c | sort -rn | head
```

**3. Real-time Monitoring**
```bash
# Multiple logs at once
tail -f /var/log/syslog /var/log/auth.log

# With filtering
tail -f /var/log/syslog | grep --line-buffered "error"

# Colored real-time monitoring
tail -f /var/log/syslog | grep --color=always -E "error|warning|$"
```

**4. Log Analysis Scripts**
```bash
# Top 10 error messages
grep -i error /var/log/syslog | awk '{$1=$2=$3=$4=""; print $0}' | sort | uniq -c | sort -rn | head -10

# Hourly log distribution
awk '{print $3}' /var/log/syslog | cut -d: -f1 | sort | uniq -c

# HTTP status code distribution
awk '{print $9}' /var/log/nginx/access.log | sort | uniq -c | sort -rn

# Top requested URLs
awk '{print $7}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -20
```

---

## Shell Scripting Essentials

### Basic Scripting
```bash
#!/bin/bash

# Variables
NAME="value"
echo $NAME

# Command substitution
RESULT=$(command)

# Arguments
$0  # Script name
$1, $2  # Arguments
$#  # Number of arguments
$@  # All arguments

# Conditionals
if [ condition ]; then
    echo "true"
fi

# Loops
for i in 1 2 3; do
    echo $i
done

# Functions
my_function() {
    echo "Hello $1"
}
```

### 🎁 Easter Eggs & Power Tips

**1. Parameter Expansion Magic**
```bash
# Default values
${VAR:-default}  # Use default if unset
${VAR:=default}  # Set to default if unset

# String operations
${#VAR}           # Length
${VAR:offset:len} # Substring
${VAR#pattern}    # Remove from start
${VAR%pattern}    # Remove from end
${VAR//old/new}   # Replace all

# Examples
FILE="/path/to/file.txt"
${FILE##*/}   # file.txt (basename)
${FILE%.*}    # /path/to/file (no extension)
${FILE##*.}   # txt (extension only)
```

**2. Advanced Conditionals**
```bash
# Double brackets (better)
[[ -f file ]]
[[ $A == pattern* ]]  # Pattern matching
[[ $A =~ regex ]]     # Regex matching

# Logical operators
[[ condition1 && condition2 ]]
[[ condition1 || condition2 ]]

# Case statement
case $VAR in
    pattern1) echo "match 1";;
    pattern2) echo "match 2";;
    *) echo "no match";;
esac

# One-liners
[ condition ] && command_if_true
command && echo "success" || echo "failure"
```

**3. Array Operations**
```bash
# Declaration
ARRAY=(one two three)

# Access
echo ${ARRAY[0]}
echo ${ARRAY[@]}    # All elements
echo ${#ARRAY[@]}   # Length

# Iterate
for item in "${ARRAY[@]}"; do
    echo "$item"
done

# Associative arrays
declare -A HASH
HASH[key]="value"
echo ${HASH[key]}
```

**4. Error Handling**
```bash
# Exit on error
set -e

# Exit on undefined variable
set -u

# Exit on pipe failure
set -o pipefail

# Combine all
set -euo pipefail

# Trap errors
trap 'echo "Error on line $LINENO"' ERR

# Cleanup on exit
cleanup() {
    rm -f /tmp/tempfile
}
trap cleanup EXIT
```

**5. Useful One-Liners**
```bash
# Current script directory
DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"

# Check if running as root
[ $(id -u) -eq 0 ] || { echo "Must run as root"; exit 1; }

# Yes/No prompt
read -p "Continue? (y/n) " -n 1 -r
[[ $REPLY =~ ^[Yy]$ ]] && echo "Continuing..."

# Progress indicator
for i in {1..10}; do
    echo -ne "Progress: $i/10\r"
    sleep 1
done
```

---

## Performance & Debugging

### Basic Tools
```bash
# CPU usage
top
htop

# Memory usage
free -h

# Disk I/O
iostat -x 1

# System call trace
strace command

# Open files
lsof
```

### 🎁 Easter Eggs & Power Tips

**1. strace Mastery**
```bash
# Trace specific system calls
strace -e open,read,write command

# Trace with timestamps
strace -tt command

# Count calls and time
strace -c command

# Follow child processes
strace -f command

# Attach to running process
strace -p PID

# Find config files a program reads
strace -e openat program 2>&1 | grep -E '\.conf|\.config'
```

**2. lsof Power Moves**
```bash
# By user
lsof -u username

# By process
lsof -p PID

# Network connections
lsof -i :80

# Listening ports
lsof -i -s TCP:LISTEN

# Files in directory
lsof +D /path

# Who's using a file
lsof /path/to/file

# Deleted but still open files
lsof | grep deleted

# Repeat mode (monitoring)
lsof -r 1
```

**3. Performance Profiling**
```bash
# CPU profiling
perf top
perf record -p PID

# Memory profiling
valgrind --leak-check=full command

# Per-process CPU usage
pidstat -p PID 1

# I/O per process
pidstat -d 1

# Time command execution
time command
time -v command  # Verbose
```

**4. Memory Analysis**
```bash
# Per-process memory
pmap -x PID

# Memory by user
ps aux --sort=-%mem | head

# OOM killer logs
journalctl -k | grep -i "killed process"

# Memory mapped files
cat /proc/PID/maps

# Check for memory leaks
valgrind --leak-check=full program
```

**5. Network Performance**
```bash
# Bandwidth test
iperf3 -c server

# Network throughput
iftop -i eth0

# Packet loss
ping -c 100 host | grep "packet loss"

# TCP window size
ss -ti

# Dropped packets
netstat -i
```

---

## Common Pitfalls & Best Practices

### Shell Scripting
- Always quote variables: `"$VAR"` not `$VAR`
- Check exit codes: `$?` after commands
- Use `[[` instead of `[` for conditionals
- Quote arrays: `"${ARRAY[@]}"`

### File Operations
- Test commands with `-n` or echo first
- Watch for spaces in filenames
- Remember Linux is case-sensitive
- Double-check paths before `rm -rf`

### Networking
- Specify interface with `-i` when needed
- Remember firewall rules
- Check SELinux when troubleshooting
- Use UUID in fstab, not device names

### Package Management
- Always update before install
- Check for lock files if stuck
- Verify repository connectivity
- Be careful with PPAs and third-party repos

### Performance
- Try SIGTERM before SIGKILL (-9)
- Monitor trends, not just snapshots
- Check all resources: CPU, memory, disk, network
- Log everything for post-mortem analysis

---

## Quick Reference Tables

### File Permissions
| Octal | Binary | Permission |
|-------|--------|------------|
| 7 | 111 | rwx |
| 6 | 110 | rw- |
| 5 | 101 | r-x |
| 4 | 100 | r-- |
| 3 | 011 | -wx |
| 2 | 010 | -w- |
| 1 | 001 | --x |
| 0 | 000 | --- |

### Common Signals
| Signal | Number | Description |
|--------|--------|-------------|
| SIGHUP | 1 | Hangup (reload config) |
| SIGINT | 2 | Interrupt (Ctrl+C) |
| SIGQUIT | 3 | Quit |
| SIGKILL | 9 | Kill (cannot be caught) |
| SIGTERM | 15 | Terminate (graceful) |
| SIGSTOP | 19 | Stop process |
| SIGCONT | 18 | Continue process |

### Journalctl Priorities
| Priority | Level |
|----------|-------|
| 0 | emerg |
| 1 | alert |
| 2 | crit |
| 3 | err |
| 4 | warning |
| 5 | notice |
| 6 | info |
| 7 | debug |

---

## Additional Resources

### Man Pages
- `man command` - Command manual
- `man 5 file` - File format manual
- `apropos keyword` - Search man pages
- `whatis command` - One-line description

### Help Commands
- `command --help` - Quick help
- `info command` - GNU info pages
- `help builtin` - Bash builtin help

### Online Resources
- explainshell.com - Command breakdown
- tldr.sh - Simplified man pages
- devhints.io - Quick references

---

*This reference guide is meant to be a living document. Bookmark it, customize it, and keep discovering new techniques!*
