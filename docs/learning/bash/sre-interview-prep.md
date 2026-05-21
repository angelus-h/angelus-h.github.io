# Bash for SRE - Interview Preparation

**Goal:** Prepare for live scripting interviews for SRE positions  
**Time Required:** ~15-18 hours (2-3 weeks, 1 hour/day)  
**Level:** Beginner → Interview-ready  
**Focus:** Operational scripting, system administration, automation

---

## Why This Learning Plan?

### What this guide will NOT do:
- Deep Unix internals theory  
- "Become a DevOps engineer" comprehensive approach  
- General shell scripting for all use cases  
- Complex sed/awk mastery (basics only)

### What THIS guide WILL do:
- **SRE-specific Bash scripting** - operational automation  
- **Live coding confidence** - command fluency, quick scripts  
- **Interview patterns** - working solution in 30 minutes  
- **System administration skills** - logs, processes, disk, network  
- **Real-world SRE tasks** - health checks, monitoring, incident response

### What this prepares you for:
- **Live scripting exercises** (common in SRE interviews)
- **Technical screening tasks** (debugging, automation)
- **On-call scenarios** (quick one-liners, diagnostic scripts)
- **Production troubleshooting** (realistic problem-solving)

---

## Learning Methodology

### Progressive Approach (3 phases):

**Week 1: Claude-Assisted Learning**
- Claude writes the solution (full script)
- YOU read, understand, execute it
- Rewrite it YOURSELF (without Claude, but you can reference)
- Goal: **Command familiarity, pattern recognition**

**Week 2: Guided Practice**
- Task description
- YOU write it (Claude does NOT help initially!)
- If stuck → Claude hint (NOT full solution!)
- Testing in terminal
- Goal: **Independent problem-solving**

**Week 3: Timed Mock Interviews**
- Timer: 30 minutes / task
- Think out loud (as if in interview)
- No help (realistic interview conditions)
- Goal: **Interview simulation, time pressure handling**

### Daily Routine (1 hour):
1. **Reading** (10 min) - module theory, command syntax
2. **Example study** (10 min) - script comprehension
3. **Hands-on practice** (30 min) - execute commands, write scripts
4. **Review** (10 min) - what worked, what didn't, debugging notes

---

## Module 1: Bash Basics

**Time Required:** ~1 hour  
**Goal:** Variables, command substitution, basic syntax

### 1.1 Variables and String Basics

**Variable assignment (NO spaces around =!):**

```bash
# String variables
pod_name="nginx-deployment-7d5c8f9b6d-abc12"
namespace="production"

# Numeric variables (still stored as strings)
replica_count=3
timeout_seconds=30

# Using variables ($ prefix)
echo "Pod name: $pod_name"
echo "Namespace: $namespace"

# Preferred: curly braces (safer, clearer)
echo "Pod name: ${pod_name}"
echo "Checking namespace: ${namespace}"
```

**Command substitution (running commands, storing output):**

```bash
# Modern syntax (preferred)
current_date=$(date +%Y-%m-%d)
pod_count=$(kubectl get pods --no-headers | wc -l)

# Old syntax (avoid, but you'll see it)
hostname=`hostname`

# Usage
echo "Current date: ${current_date}"
echo "Total pods: ${pod_count}"
```

**Quotes matter:**

```bash
# Double quotes: variable expansion happens
echo "Pod name: $pod_name"  # Output: Pod name: nginx-deployment-...

# Single quotes: literal string, NO expansion
echo 'Pod name: $pod_name'  # Output: Pod name: $pod_name

# No quotes: word splitting, globbing
files="file1.txt file2.txt"
echo $files   # OK here
rm $files     # DANGEROUS if filenames have spaces!
```

### 1.2 Reading Input and Basic Output

```bash
# Reading user input
read -p "Enter namespace: " namespace
echo "You entered: ${namespace}"

# Reading with timeout
read -t 5 -p "Enter pod name (5 sec timeout): " pod_name

# Reading passwords (hidden input)
read -sp "Enter password: " password
echo  # Newline after hidden input

# Output redirection
echo "Log entry" > log.txt          # Overwrite file
echo "Another entry" >> log.txt     # Append to file
echo "Error occurred" >&2           # Print to stderr
```

### 1.3 Exit Codes and Command Success

**Every command returns an exit code:**
- `0` = success
- `1-255` = error (specific meaning varies)

```bash
# Check last command's exit code
kubectl get pods
echo $?  # 0 if successful, non-zero if error

# Explicit exit codes in scripts
exit 0   # Success
exit 1   # Generic error
exit 2   # Misuse of command

# Using exit codes in conditions
if kubectl get pod nginx-abc12 &>/dev/null; then
    echo "Pod exists"
else
    echo "Pod not found"
fi
```

### 1.4 Practice Exercises (30 minutes)

**Exercise 1: Variable Practice (EASY)**

```bash
# Task:
# 1. Create variables: namespace="production", pod_count=5
# 2. Print: "Namespace production has 5 pods"
# 3. Use command substitution to get current user: $(whoami)
# 4. Print: "Script run by: <username>"
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

namespace="production"
pod_count=5

echo "Namespace ${namespace} has ${pod_count} pods"

current_user=$(whoami)
echo "Script run by: ${current_user}"
```
</details>

**Exercise 2: Command Substitution (MEDIUM)**

```bash
# Task:
# Get the number of files in /tmp directory
# Store in a variable
# Print: "/tmp contains X files"
# Hint: ls /tmp | wc -l
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

file_count=$(ls /tmp | wc -l)
echo "/tmp contains ${file_count} files"

# Alternative (more robust):
file_count=$(find /tmp -maxdepth 1 -type f | wc -l)
```
</details>

---

## Module 2: Control Flow

**Time Required:** ~1 hour  
**Goal:** if/else, case, loops - decision making in scripts

### 2.1 If Statements

**Basic syntax:**

```bash
# Simple if
if [ -f "/var/log/app.log" ]; then
    echo "Log file exists"
fi

# If-else
if [ $replica_count -lt 3 ]; then
    echo "WARNING: Low replica count"
else
    echo "Replica count OK"
fi

# If-elif-else
if [ $cpu_usage -gt 90 ]; then
    echo "CRITICAL: High CPU"
elif [ $cpu_usage -gt 70 ]; then
    echo "WARNING: Elevated CPU"
else
    echo "CPU normal"
fi
```

**Test operators ([ ] or [[ ]]):**

```bash
# File tests
[ -f file.txt ]    # File exists and is regular file
[ -d /tmp ]        # Directory exists
[ -r file.txt ]    # File is readable
[ -w file.txt ]    # File is writable
[ -x script.sh ]   # File is executable

# String tests
[ -z "$var" ]      # String is empty
[ -n "$var" ]      # String is NOT empty
[ "$a" = "$b" ]    # Strings are equal
[ "$a" != "$b" ]   # Strings are NOT equal

# Numeric tests
[ $a -eq $b ]      # Equal
[ $a -ne $b ]      # Not equal
[ $a -gt $b ]      # Greater than
[ $a -lt $b ]      # Less than
[ $a -ge $b ]      # Greater than or equal
[ $a -le $b ]      # Less than or equal

# Logical operators
[ $a -gt 5 ] && [ $a -lt 10 ]   # AND
[ $a -lt 5 ] || [ $a -gt 10 ]   # OR
! [ -f file.txt ]               # NOT
```

**Modern [[ ]] vs old [ ]:**

```bash
# [[ ]] is better: pattern matching, no word splitting
if [[ $pod_status == "Running" ]]; then
    echo "Pod is running"
fi

# Pattern matching
if [[ $pod_name == nginx-* ]]; then
    echo "This is an nginx pod"
fi

# Regex matching
if [[ $log_line =~ ERROR|CRITICAL ]]; then
    echo "Found error in log"
fi
```

### 2.2 Case Statements

**Switch-like behavior:**

```bash
# Basic case
pod_status="Running"

case $pod_status in
    "Running")
        echo "Pod is healthy"
        ;;
    "Pending")
        echo "Pod is starting"
        ;;
    "CrashLoopBackOff"|"Error")
        echo "Pod has issues"
        ;;
    *)
        echo "Unknown status"
        ;;
esac
```

**SRE example - log level handling:**

```bash
#!/bin/bash

log_level="$1"

case $log_level in
    ERROR|CRITICAL)
        echo "High severity - alerting on-call"
        # Send alert
        ;;
    WARNING)
        echo "Medium severity - logging to file"
        # Log to file
        ;;
    INFO|DEBUG)
        echo "Low severity - ignoring"
        ;;
    *)
        echo "Unknown log level: $log_level"
        exit 1
        ;;
esac
```

### 2.3 Loops

**For loops:**

```bash
# Iterate over list
for namespace in default kube-system production; do
    echo "Checking namespace: $namespace"
done

# Iterate over command output
for pod in $(kubectl get pods -o name); do
    echo "Pod: $pod"
done

# C-style for loop
for ((i=1; i<=5; i++)); do
    echo "Attempt $i"
done

# Iterate over files
for logfile in /var/log/*.log; do
    echo "Processing: $logfile"
done
```

**While loops:**

```bash
# Retry logic
attempts=0
max_attempts=3

while [ $attempts -lt $max_attempts ]; do
    echo "Attempt $((attempts + 1))"
    
    if kubectl get pod nginx-abc12 &>/dev/null; then
        echo "Pod found!"
        break
    fi
    
    attempts=$((attempts + 1))
    sleep 5
done
```

**Reading files line by line (IMPORTANT pattern!):**

```bash
# Read log file line by line
while IFS= read -r line; do
    if [[ $line == *ERROR* ]]; then
        echo "Found error: $line"
    fi
done < /var/log/app.log

# Or with pipe:
cat /var/log/app.log | while read -r line; do
    echo "Processing: $line"
done
```

### 2.4 Practice Exercises (30 minutes)

**Exercise 1: Namespace Checker (EASY)**

```bash
# Task:
# Ask user for namespace name
# Check if namespace exists: kubectl get namespace <name>
# If exists → print "Namespace exists"
# If not → print "Namespace not found"
# Hint: Use exit code check
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

read -p "Enter namespace name: " namespace

if kubectl get namespace "$namespace" &>/dev/null; then
    echo "Namespace exists"
else
    echo "Namespace not found"
fi
```
</details>

**Exercise 2: Pod Status Report (MEDIUM)**

```bash
# Task:
# Loop through pods in "default" namespace
# For each pod, print: "Pod <name>: <status>"
# Hint: kubectl get pods -n default -o custom-columns=NAME:.metadata.name,STATUS:.status.phase --no-headers
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

kubectl get pods -n default -o custom-columns=NAME:.metadata.name,STATUS:.status.phase --no-headers | while read -r name status; do
    echo "Pod $name: $status"
done
```
</details>

---

## Module 3: Text Processing (grep, sed, awk)

**Time Required:** ~1.5 hours  
**Goal:** Parse logs, filter output, extract data (CRITICAL SRE skill!)

### 3.1 grep - Searching and Filtering

**Basic usage:**

```bash
# Search for pattern in file
grep "ERROR" /var/log/app.log

# Case-insensitive search
grep -i "error" /var/log/app.log

# Show line numbers
grep -n "ERROR" /var/log/app.log

# Invert match (show lines NOT matching)
grep -v "INFO" /var/log/app.log

# Count matching lines
grep -c "ERROR" /var/log/app.log

# Show context (lines before/after match)
grep -A 3 "ERROR" /var/log/app.log  # 3 lines after
grep -B 2 "ERROR" /var/log/app.log  # 2 lines before
grep -C 2 "ERROR" /var/log/app.log  # 2 lines before and after
```

**Advanced patterns:**

```bash
# Multiple patterns
grep -E "ERROR|CRITICAL|FATAL" /var/log/app.log

# Regex patterns
grep -E "^\[ERROR\]" /var/log/app.log  # Lines starting with [ERROR]
grep -E "[0-9]{3,}" /var/log/app.log   # Lines with 3+ digit numbers

# Recursive search in directory
grep -r "connection refused" /var/log/

# Only show filenames with matches
grep -l "ERROR" /var/log/*.log
```

**SRE example - find failed pods:**

```bash
#!/bin/bash

# Find pods with Error or CrashLoopBackOff status
kubectl get pods --all-namespaces | grep -E "Error|CrashLoopBackOff"

# Count errors in last hour of logs
journalctl --since "1 hour ago" | grep -c "ERROR"
```

### 3.2 sed - Stream Editing

**Basic replacements:**

```bash
# Replace first occurrence per line
sed 's/old/new/' file.txt

# Replace all occurrences (global flag)
sed 's/old/new/g' file.txt

# Replace in-place (modify original file)
sed -i 's/old/new/g' file.txt

# Case-insensitive replacement
sed 's/error/ERROR/gi' file.txt
```

**Deleting lines:**

```bash
# Delete lines matching pattern
sed '/DEBUG/d' /var/log/app.log

# Delete empty lines
sed '/^$/d' file.txt

# Delete lines 5-10
sed '5,10d' file.txt
```

**Extracting specific lines:**

```bash
# Print only matching lines
sed -n '/ERROR/p' /var/log/app.log

# Print line range
sed -n '10,20p' file.txt
```

**SRE example - clean log file:**

```bash
#!/bin/bash

# Remove DEBUG lines and timestamps
sed -e '/DEBUG/d' -e 's/^[0-9T:-]* //' /var/log/app.log > cleaned.log
```

### 3.3 awk - Pattern Scanning and Processing

**Column extraction:**

```bash
# Print specific columns (space-delimited)
echo "pod-1 Running 3" | awk '{print $1, $2}'  # pod-1 Running

# Print last column
kubectl get pods | awk '{print $NF}'

# Print with custom delimiter
awk -F':' '{print $1}' /etc/passwd  # Print usernames
```

**Filtering and conditions:**

```bash
# Print lines where column 3 > 5
kubectl get pods | awk '$3 > 5 {print $1}'

# Print if column matches pattern
kubectl get pods | awk '$3 == "Running" {print $1}'

# Sum a column
df -h | awk '{sum += $5} END {print sum}'
```

**SRE examples:**

```bash
# Extract pod names with more than 3 restarts
kubectl get pods | awk '$4 > 3 {print $1}'

# Calculate average CPU usage
top -bn1 | grep "Cpu(s)" | awk '{print $2}' | sed 's/%us,//'

# Parse JSON-like output
echo '{"name":"nginx","status":"Running"}' | awk -F'"' '{print $4, $8}'
```

### 3.4 Combining grep, sed, awk (Pipelines)

**Common SRE patterns:**

```bash
# Find ERROR lines, extract timestamp and message
grep ERROR /var/log/app.log | awk '{print $1, $2, $5, $6, $7}'

# Count unique error types
grep ERROR /var/log/app.log | awk '{print $5}' | sort | uniq -c

# Find top 10 IP addresses in access log
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10

# Extract failed pod names
kubectl get pods --all-namespaces | grep -v Running | awk '{print $2}'
```

### 3.5 Practice Exercises (30 minutes)

**Exercise 1: Error Counter (EASY)**

```bash
# Task:
# Count how many ERROR lines are in /var/log/syslog
# Print: "Total errors: X"
# Hint: grep + wc -l
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

error_count=$(grep -c "ERROR" /var/log/syslog 2>/dev/null || echo 0)
echo "Total errors: $error_count"
```
</details>

**Exercise 2: Pod Name Extractor (MEDIUM)**

```bash
# Task:
# Get all pod names (only names, no headers) in "default" namespace
# One name per line
# Hint: kubectl get pods + awk
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

kubectl get pods -n default --no-headers | awk '{print $1}'

# Alternative:
kubectl get pods -n default -o custom-columns=NAME:.metadata.name --no-headers
```
</details>

**Exercise 3: Log Error Report (HARD)**

```bash
# Task:
# Create a report from /var/log/app.log:
# 1. Count total ERROR lines
# 2. Count total WARNING lines
# 3. Extract first 5 ERROR messages (just the message part, not timestamp)
# Output format:
#   Errors: X
#   Warnings: Y
#   Sample errors:
#   - <error message 1>
#   - <error message 2>
#   ...
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

logfile="/var/log/app.log"

# Count errors and warnings
error_count=$(grep -c "ERROR" "$logfile" 2>/dev/null || echo 0)
warning_count=$(grep -c "WARNING" "$logfile" 2>/dev/null || echo 0)

echo "Errors: $error_count"
echo "Warnings: $warning_count"
echo "Sample errors:"

# Extract first 5 error messages (assuming format: TIMESTAMP LEVEL MESSAGE)
grep "ERROR" "$logfile" | head -5 | awk '{$1=""; $2=""; print "- " $0}'
```
</details>

---

## Module 4: Functions and Script Organization

**Time Required:** ~1 hour  
**Goal:** Code reusability, modular scripts

### 4.1 Function Basics

**Syntax:**

```bash
# Simple function
function greet() {
    echo "Hello from function!"
}

# Alternative syntax (POSIX-compliant)
greet() {
    echo "Hello from function!"
}

# Calling function
greet

# Function with parameters
check_pod() {
    local pod_name="$1"
    local namespace="$2"
    
    echo "Checking pod: $pod_name in namespace: $namespace"
}

# Call with arguments
check_pod "nginx-abc12" "production"
```

**Return values (exit codes):**

```bash
# Functions return exit codes (0-255)
is_pod_running() {
    local pod="$1"
    
    if kubectl get pod "$pod" &>/dev/null; then
        return 0  # Success
    else
        return 1  # Failure
    fi
}

# Use in conditions
if is_pod_running "nginx-abc12"; then
    echo "Pod is running"
else
    echo "Pod not found"
fi
```

**Returning strings (echo and command substitution):**

```bash
get_pod_status() {
    local pod="$1"
    
    status=$(kubectl get pod "$pod" -o jsonpath='{.status.phase}' 2>/dev/null)
    echo "$status"
}

# Capture output
pod_status=$(get_pod_status "nginx-abc12")
echo "Status: $pod_status"
```

### 4.2 Local vs Global Variables

```bash
#!/bin/bash

# Global variable
global_var="I am global"

my_function() {
    # Local variable (only exists in function)
    local local_var="I am local"
    
    # Can access global
    echo "Function sees global: $global_var"
    
    # Modify global
    global_var="Modified in function"
}

my_function
echo "After function: $global_var"  # Modified in function
echo "Local var outside: $local_var"  # Empty (doesn't exist)
```

**Best practice: Always use `local` for function variables!**

### 4.3 SRE Function Examples

**Health check function:**

```bash
#!/bin/bash

check_url_health() {
    local url="$1"
    local timeout="${2:-5}"  # Default 5 seconds
    
    if curl -sf --max-time "$timeout" "$url" >/dev/null; then
        echo "healthy"
        return 0
    else
        echo "unhealthy"
        return 1
    fi
}

# Usage
status=$(check_url_health "https://example.com")
echo "Status: $status"

if check_url_health "https://google.com" 10; then
    echo "Google is reachable"
fi
```

**Pod health check:**

```bash
#!/bin/bash

check_pod_health() {
    local pod="$1"
    local namespace="${2:-default}"
    
    # Get pod status
    status=$(kubectl get pod "$pod" -n "$namespace" \
             -o jsonpath='{.status.phase}' 2>/dev/null)
    
    # Get restart count
    restarts=$(kubectl get pod "$pod" -n "$namespace" \
               -o jsonpath='{.status.containerStatuses[0].restartCount}' 2>/dev/null)
    
    if [[ -z "$status" ]]; then
        echo "Pod not found"
        return 2
    elif [[ "$status" != "Running" ]]; then
        echo "Unhealthy (Status: $status)"
        return 1
    elif [[ $restarts -gt 3 ]]; then
        echo "Degraded (Restarts: $restarts)"
        return 1
    else
        echo "Healthy"
        return 0
    fi
}

# Usage
health=$(check_pod_health "nginx-abc12" "production")
echo "Pod health: $health"
```

### 4.4 Practice Exercises (30 minutes)

**Exercise 1: Temperature Converter (EASY)**

```bash
# Task:
# Write a function celsius_to_fahrenheit
# Input: Celsius temperature
# Output: Fahrenheit temperature
# Formula: F = (C * 9/5) + 32
# Test: celsius_to_fahrenheit 0  # Should output 32
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

celsius_to_fahrenheit() {
    local celsius="$1"
    local fahrenheit=$(echo "scale=2; ($celsius * 9/5) + 32" | bc)
    echo "$fahrenheit"
}

# Test
result=$(celsius_to_fahrenheit 0)
echo "0°C = ${result}°F"

result=$(celsius_to_fahrenheit 100)
echo "100°C = ${result}°F"
```
</details>

**Exercise 2: File Exists Checker (MEDIUM)**

```bash
# Task:
# Write function check_file that:
# - Takes filename as argument
# - Returns 0 if file exists and is readable
# - Returns 1 if file doesn't exist
# - Returns 2 if file exists but not readable
# - Echoes appropriate message in each case
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

check_file() {
    local filename="$1"
    
    if [[ ! -e "$filename" ]]; then
        echo "File does not exist"
        return 1
    elif [[ ! -r "$filename" ]]; then
        echo "File exists but not readable"
        return 2
    else
        echo "File exists and is readable"
        return 0
    fi
}

# Test
check_file "/etc/passwd"
echo "Exit code: $?"

check_file "/nonexistent"
echo "Exit code: $?"
```
</details>

---

## Module 5: Error Handling and Debugging

**Time Required:** ~1 hour  
**Goal:** Robust scripts, debugging techniques (CRITICAL for production!)

### 5.1 Exit Codes and Error Handling

**Check command success:**

```bash
#!/bin/bash

# Method 1: Check $? immediately after command
kubectl get pods
if [ $? -eq 0 ]; then
    echo "Command succeeded"
else
    echo "Command failed"
fi

# Method 2: Use command in if directly (preferred)
if kubectl get pods; then
    echo "Command succeeded"
else
    echo "Command failed"
fi

# Method 3: Use || and && operators
kubectl get pods || echo "Failed to get pods"
kubectl get pods && echo "Successfully got pods"
```

**Set error handling options:**

```bash
#!/bin/bash

# Exit on error (any command fails)
set -e

# Exit on undefined variable
set -u

# Exit on pipe failure
set -o pipefail

# Combine all (common pattern)
set -euo pipefail

# Example:
set -euo pipefail

kubectl get pods  # If this fails, script exits immediately
echo "This won't run if kubectl failed"
```

**Trap errors (cleanup on exit):**

```bash
#!/bin/bash

# Cleanup function
cleanup() {
    echo "Cleaning up..."
    rm -f /tmp/temp_file.txt
}

# Run cleanup on exit (success or failure)
trap cleanup EXIT

# Run cleanup on error
trap 'echo "Error occurred on line $LINENO"' ERR

# Your script logic
echo "Running script..."
# ... commands ...
```

### 5.2 Logging and Debugging

**Debug output:**

```bash
#!/bin/bash

# Enable debug mode (print each command before execution)
set -x

kubectl get pods
echo "After kubectl"

# Disable debug mode
set +x

# Conditional debug
DEBUG=${DEBUG:-false}

debug() {
    if [[ "$DEBUG" == "true" ]]; then
        echo "[DEBUG] $*" >&2
    fi
}

debug "This is a debug message"
debug "Pod count: $(kubectl get pods --no-headers | wc -l)"

# Run with: DEBUG=true ./script.sh
```

**Logging to file:**

```bash
#!/bin/bash

LOGFILE="/var/log/myscript.log"

log() {
    local level="$1"
    shift
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] [$level] $*" | tee -a "$LOGFILE"
}

log "INFO" "Script started"
log "ERROR" "Failed to connect to database"
log "WARNING" "High CPU usage detected"
```

### 5.3 Input Validation

```bash
#!/bin/bash

# Check argument count
if [ $# -ne 2 ]; then
    echo "Usage: $0 <namespace> <pod_name>"
    exit 1
fi

namespace="$1"
pod_name="$2"

# Validate namespace format (alphanumeric and hyphens only)
if [[ ! "$namespace" =~ ^[a-z0-9-]+$ ]]; then
    echo "Error: Invalid namespace format"
    exit 1
fi

# Validate pod exists
if ! kubectl get pod "$pod_name" -n "$namespace" &>/dev/null; then
    echo "Error: Pod $pod_name not found in namespace $namespace"
    exit 1
fi

echo "Validation passed"
```

### 5.4 Practice Exercises (30 minutes)

**Exercise 1: Safe File Deletion (EASY)**

```bash
# Task:
# Write a script that:
# - Takes filename as argument
# - Checks if file exists
# - If exists: delete it and print "Deleted: <filename>"
# - If not: print "File not found" and exit with code 1
# Use proper error handling!
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

set -u

if [ $# -ne 1 ]; then
    echo "Usage: $0 <filename>"
    exit 1
fi

filename="$1"

if [[ ! -f "$filename" ]]; then
    echo "File not found: $filename"
    exit 1
fi

rm "$filename"
echo "Deleted: $filename"
```
</details>

**Exercise 2: Retry Logic (MEDIUM)**

```bash
# Task:
# Write a function that:
# - Tries to execute a command up to 3 times
# - Waits 2 seconds between attempts
# - Returns 0 if command succeeds
# - Returns 1 if all attempts fail
# Test with: retry_command "kubectl get pods"
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

retry_command() {
    local max_attempts=3
    local attempt=1
    
    while [ $attempt -le $max_attempts ]; do
        echo "Attempt $attempt of $max_attempts"
        
        if eval "$@"; then
            echo "Command succeeded"
            return 0
        fi
        
        if [ $attempt -lt $max_attempts ]; then
            echo "Command failed, retrying in 2 seconds..."
            sleep 2
        fi
        
        attempt=$((attempt + 1))
    done
    
    echo "All attempts failed"
    return 1
}

# Test
retry_command kubectl get pods
```
</details>

---

## Module 6: System Administration Tasks

**Time Required:** ~1.5 hours  
**Goal:** Disk, processes, services, networking - essential SRE skills

### 6.1 Disk Usage and Management

```bash
# Check disk usage
df -h                    # Human-readable disk usage
df -h /var               # Specific mount point

# Disk usage by directory
du -sh /var/log          # Summary of directory size
du -h /var/log | sort -h # Sort by size

# Find large files
find /var/log -type f -size +100M  # Files larger than 100MB

# Find files modified in last 24 hours
find /var/log -type f -mtime -1

# Disk I/O stats
iostat -x 1 5            # Extended stats, 1 sec interval, 5 times
```

**SRE example - disk cleanup script:**

```bash
#!/bin/bash

set -euo pipefail

LOGDIR="/var/log"
THRESHOLD=80

# Check disk usage percentage
usage=$(df -h "$LOGDIR" | awk 'NR==2 {print $5}' | sed 's/%//')

if [ "$usage" -gt "$THRESHOLD" ]; then
    echo "WARNING: Disk usage at ${usage}%"
    
    # Find and delete log files older than 7 days
    find "$LOGDIR" -name "*.log" -type f -mtime +7 -delete
    
    echo "Cleaned up old log files"
else
    echo "Disk usage OK: ${usage}%"
fi
```

### 6.2 Process Management

```bash
# List processes
ps aux                   # All processes
ps aux | grep nginx      # Filter by name

# Process tree
pstree                   # Show process tree
pstree -p                # Include PIDs

# Top processes by CPU/memory
top -bn1 | head -20      # Batch mode, 1 iteration
htop                     # Interactive (if installed)

# Find process by name
pgrep nginx              # Get PIDs
pkill nginx              # Kill by name

# Process info
ps -p 1234 -o pid,cmd,cpu,mem  # Specific process info
```

**SRE example - check high memory processes:**

```bash
#!/bin/bash

# Get top 5 processes by memory usage
echo "Top 5 memory consumers:"
ps aux --sort=-%mem | head -6 | awk '{printf "%-20s %8s %8s\n", $11, $4, $6}'

# Alert if any process uses > 50% memory
while read -r pid mem cmd; do
    if (( $(echo "$mem > 50" | bc -l) )); then
        echo "ALERT: Process $cmd (PID $pid) using ${mem}% memory"
    fi
done < <(ps aux | awk 'NR>1 {print $2, $4, $11}')
```

### 6.3 Service Management (systemd)

```bash
# Check service status
systemctl status nginx
systemctl is-active nginx    # Returns active/inactive
systemctl is-enabled nginx   # Returns enabled/disabled

# Start/stop/restart services
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx   # Reload config without restart

# Enable/disable on boot
sudo systemctl enable nginx
sudo systemctl disable nginx

# View service logs
journalctl -u nginx
journalctl -u nginx -f       # Follow logs (tail -f style)
journalctl -u nginx --since "1 hour ago"
journalctl -u nginx -n 50    # Last 50 lines
```

**SRE example - service health check:**

```bash
#!/bin/bash

check_service() {
    local service="$1"
    
    if systemctl is-active "$service" &>/dev/null; then
        echo "$service: Running"
        return 0
    else
        echo "$service: NOT running"
        
        # Check last 10 log lines for errors
        echo "Recent errors:"
        journalctl -u "$service" -n 10 --no-pager | grep -i error
        
        return 1
    fi
}

# Check critical services
for service in nginx postgresql redis; do
    check_service "$service"
done
```

### 6.4 Network Diagnostics

```bash
# Network interfaces
ip addr                  # Show IP addresses
ip link                  # Show network interfaces
ip route                 # Show routing table

# Check connectivity
ping -c 4 8.8.8.8       # Ping Google DNS
curl -I https://google.com  # HTTP check

# Port checking
nc -zv localhost 80      # Check if port 80 is open
lsof -i :80              # What's listening on port 80
ss -tuln                 # Show listening ports

# DNS lookup
nslookup google.com
dig google.com
host google.com
```

**SRE example - connectivity check:**

```bash
#!/bin/bash

check_connectivity() {
    local host="$1"
    local port="${2:-80}"
    local timeout=5
    
    if nc -z -w "$timeout" "$host" "$port" 2>/dev/null; then
        echo "$host:$port is reachable"
        return 0
    else
        echo "$host:$port is NOT reachable"
        return 1
    fi
}

# Check critical endpoints
check_connectivity "database.internal" 5432
check_connectivity "redis.internal" 6379
check_connectivity "api.example.com" 443
```

### 6.5 Practice Exercises (30 minutes)

**Exercise 1: High CPU Alert (EASY)**

```bash
# Task:
# Check current CPU usage (user + system)
# If > 80%, print "HIGH CPU USAGE"
# Otherwise print "CPU OK"
# Hint: top -bn1 | grep "Cpu(s)"
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}' | cut -d'.' -f1)

if [ "$cpu_usage" -gt 80 ]; then
    echo "HIGH CPU USAGE: ${cpu_usage}%"
else
    echo "CPU OK: ${cpu_usage}%"
fi
```
</details>

**Exercise 2: Service Restart Script (MEDIUM)**

```bash
# Task:
# Write a script that:
# - Takes service name as argument
# - Checks if service is running
# - If NOT running: start it
# - If running: print "Service already running"
# - Print final status
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

set -u

if [ $# -ne 1 ]; then
    echo "Usage: $0 <service_name>"
    exit 1
fi

service="$1"

if systemctl is-active "$service" &>/dev/null; then
    echo "Service $service is already running"
else
    echo "Starting $service..."
    sudo systemctl start "$service"
    
    if systemctl is-active "$service" &>/dev/null; then
        echo "Service $service started successfully"
    else
        echo "Failed to start $service"
        exit 1
    fi
fi

systemctl status "$service" --no-pager
```
</details>

---

## Module 7: Kubernetes Scripting

**Time Required:** ~1.5 hours  
**Goal:** kubectl automation, pod debugging, resource management

### 7.1 kubectl Basics for Scripting

**Getting resources:**

```bash
# List resources
kubectl get pods
kubectl get pods -n production
kubectl get pods --all-namespaces

# Output formats
kubectl get pods -o wide              # More details
kubectl get pods -o json              # JSON output
kubectl get pods -o yaml              # YAML output
kubectl get pods -o name              # Just names

# Custom columns
kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase

# No headers (for scripting)
kubectl get pods --no-headers
```

**Filtering and searching:**

```bash
# Label selectors
kubectl get pods -l app=nginx
kubectl get pods -l environment=production,tier=frontend

# Field selectors
kubectl get pods --field-selector status.phase=Running
kubectl get pods --field-selector metadata.namespace=default

# JSONPath queries
kubectl get pods -o jsonpath='{.items[*].metadata.name}'
kubectl get pods -o jsonpath='{.items[*].status.podIP}'
```

### 7.2 Pod Health Checking Scripts

**Example 1: Find unhealthy pods**

```bash
#!/bin/bash

echo "Checking for unhealthy pods..."

kubectl get pods --all-namespaces --field-selector status.phase!=Running \
    -o custom-columns=NAMESPACE:.metadata.namespace,NAME:.metadata.name,STATUS:.status.phase \
    --no-headers | while read -r ns name status; do
    echo "UNHEALTHY: Namespace=$ns, Pod=$name, Status=$status"
done
```

**Example 2: Pod restart counter**

```bash
#!/bin/bash

THRESHOLD=3

echo "Pods with high restart count (> $THRESHOLD):"

kubectl get pods --all-namespaces -o json | \
    jq -r '.items[] | 
           select(.status.containerStatuses[]?.restartCount > '$THRESHOLD') |
           "\(.metadata.namespace) \(.metadata.name) \(.status.containerStatuses[0].restartCount)"' | \
    while read -r ns name restarts; do
        echo "Namespace: $ns, Pod: $name, Restarts: $restarts"
    done
```

### 7.3 Resource Management Scripts

**Example 1: Namespace resource usage**

```bash
#!/bin/bash

namespace="${1:-default}"

echo "Resource usage in namespace: $namespace"
echo "========================================"

# CPU requests/limits
echo -e "\nCPU:"
kubectl top pods -n "$namespace" | awk 'NR>1 {print $1 "\t" $2}'

# Memory requests/limits
echo -e "\nMemory:"
kubectl top pods -n "$namespace" | awk 'NR>1 {print $1 "\t" $3}'

# Pod count
pod_count=$(kubectl get pods -n "$namespace" --no-headers | wc -l)
echo -e "\nTotal pods: $pod_count"
```

**Example 2: Find pods without resource limits**

```bash
#!/bin/bash

echo "Pods without resource limits:"

kubectl get pods --all-namespaces -o json | \
    jq -r '.items[] | 
           select(.spec.containers[].resources.limits == null) |
           "\(.metadata.namespace) \(.metadata.name)"' | \
    while read -r ns name; do
        echo "Namespace: $ns, Pod: $name"
    done
```

### 7.4 Log Collection and Analysis

```bash
#!/bin/bash

# Get logs from all pods with specific label
namespace="production"
label="app=nginx"

kubectl get pods -n "$namespace" -l "$label" -o name | while read -r pod; do
    pod_name=$(echo "$pod" | cut -d'/' -f2)
    echo "=== Logs from $pod_name ==="
    kubectl logs -n "$namespace" "$pod_name" --tail=50 | grep ERROR
    echo ""
done
```

### 7.5 Practice Exercises (30 minutes)

**Exercise 1: Pod Counter (EASY)**

```bash
# Task:
# Count pods in each namespace
# Output format:
#   default: 5
#   kube-system: 12
#   production: 8
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

kubectl get pods --all-namespaces --no-headers | \
    awk '{print $1}' | sort | uniq -c | \
    while read -r count ns; do
        echo "$ns: $count"
    done
```
</details>

**Exercise 2: Pod Health Report (MEDIUM)**

```bash
# Task:
# Generate a health report showing:
# - Total pods
# - Running pods
# - Failed/Error pods
# - Pending pods
# Output format:
#   Total: X
#   Running: Y
#   Failed: Z
#   Pending: W
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

total=$(kubectl get pods --all-namespaces --no-headers | wc -l)
running=$(kubectl get pods --all-namespaces --field-selector status.phase=Running --no-headers | wc -l)
failed=$(kubectl get pods --all-namespaces --field-selector status.phase=Failed --no-headers | wc -l)
pending=$(kubectl get pods --all-namespaces --field-selector status.phase=Pending --no-headers | wc -l)

echo "Pod Health Report"
echo "================="
echo "Total: $total"
echo "Running: $running"
echo "Failed: $failed"
echo "Pending: $pending"
```
</details>

---

## Module 8: Advanced Patterns

**Time Required:** ~1 hour  
**Goal:** Parameter expansion, arrays, advanced scripting techniques

### 8.1 Parameter Expansion

```bash
# Variable defaults
echo "${var:-default_value}"      # Use default if var unset
echo "${var:=default_value}"      # Set var to default if unset
echo "${var:+alternative}"        # Use alternative if var IS set

# String manipulation
filename="app.log"
echo "${filename%.log}"           # Remove suffix: app
echo "${filename#app.}"           # Remove prefix: log
echo "${filename/app/service}"    # Replace: service.log

# Length
echo "${#filename}"               # String length: 7

# Substring
echo "${filename:0:3}"            # First 3 chars: app
echo "${filename:4}"              # From position 4: log
```

**SRE examples:**

```bash
# Extract pod name without namespace prefix
full_name="production/nginx-abc12"
pod_name="${full_name#*/}"        # nginx-abc12

# Remove file extension
logfile="app.log"
basename="${logfile%.log}"        # app

# Default timeout value
timeout="${TIMEOUT:-30}"          # Use 30 if TIMEOUT not set
```

### 8.2 Arrays

```bash
# Array declaration
namespaces=("default" "kube-system" "production")

# Access elements
echo "${namespaces[0]}"           # default
echo "${namespaces[@]}"           # All elements
echo "${#namespaces[@]}"          # Array length

# Append to array
namespaces+=("staging")

# Loop through array
for ns in "${namespaces[@]}"; do
    echo "Namespace: $ns"
done

# Create array from command output
pods=($(kubectl get pods -o name))
```

**SRE example - multiple health checks:**

```bash
#!/bin/bash

# Array of URLs to check
urls=(
    "https://api.example.com"
    "https://db.example.com"
    "https://cache.example.com"
)

failed_checks=()

for url in "${urls[@]}"; do
    if ! curl -sf --max-time 5 "$url" >/dev/null; then
        failed_checks+=("$url")
    fi
done

if [ ${#failed_checks[@]} -gt 0 ]; then
    echo "Failed health checks:"
    printf '%s\n' "${failed_checks[@]}"
    exit 1
else
    echo "All health checks passed"
fi
```

### 8.3 Associative Arrays (Bash 4+)

```bash
# Declare associative array (dictionary/hash)
declare -A pod_counts

# Set values
pod_counts["production"]=5
pod_counts["staging"]=3
pod_counts["development"]=2

# Access values
echo "${pod_counts[production]}"

# Iterate over keys
for namespace in "${!pod_counts[@]}"; do
    count="${pod_counts[$namespace]}"
    echo "$namespace: $count pods"
done

# Iterate over values
for count in "${pod_counts[@]}"; do
    echo "Count: $count"
done
```

### 8.4 Here Documents and Here Strings

```bash
# Here document (multi-line input)
cat <<EOF > config.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
EOF

# Here string (single line input)
grep "ERROR" <<< "$log_line"

# Execute commands from here document
bash <<'SCRIPT'
echo "Running commands"
kubectl get pods
SCRIPT
```

### 8.5 Practice Exercises (30 minutes)

**Exercise 1: URL Validator (EASY)**

```bash
# Task:
# Given URL variable, extract:
# - Protocol (https)
# - Domain (example.com)
# - Path (/api/users)
# URL: "https://example.com/api/users"
# Hint: Use parameter expansion
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

url="https://example.com/api/users"

# Extract protocol
protocol="${url%%://*}"

# Remove protocol
temp="${url#*://}"

# Extract domain
domain="${temp%%/*}"

# Extract path
path="/${temp#*/}"

echo "Protocol: $protocol"
echo "Domain: $domain"
echo "Path: $path"
```
</details>

---

## Module 9: Real Interview Scenarios

**Time Required:** ~2 hours  
**Goal:** Timed practice with realistic SRE interview questions

### Scenario 1: Log Analysis (30 minutes)

**Task:**
Write a script that analyzes an application log file and:
1. Counts ERROR, WARNING, INFO lines
2. Finds the hour with most errors
3. Extracts top 5 most common error messages
4. Generates a summary report

**Sample log format:**
```
2024-01-15 10:23:45 INFO Application started
2024-01-15 10:24:12 ERROR Database connection failed
2024-01-15 10:24:15 WARNING High memory usage
```

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

set -euo pipefail

LOGFILE="${1:-/var/log/app.log}"

if [[ ! -f "$LOGFILE" ]]; then
    echo "Error: Log file not found: $LOGFILE"
    exit 1
fi

echo "Log Analysis Report"
echo "==================="
echo "File: $LOGFILE"
echo ""

# Count by level
error_count=$(grep -c "ERROR" "$LOGFILE" || echo 0)
warning_count=$(grep -c "WARNING" "$LOGFILE" || echo 0)
info_count=$(grep -c "INFO" "$LOGFILE" || echo 0)

echo "Counts:"
echo "  ERROR: $error_count"
echo "  WARNING: $warning_count"
echo "  INFO: $info_count"
echo ""

# Hour with most errors
echo "Hour with most errors:"
grep "ERROR" "$LOGFILE" | \
    awk '{print $2}' | \
    cut -d':' -f1 | \
    sort | uniq -c | \
    sort -rn | head -1 | \
    awk '{print "  " $2 ":00 - " $1 " errors"}'
echo ""

# Top 5 error messages
echo "Top 5 error messages:"
grep "ERROR" "$LOGFILE" | \
    awk '{$1=$2=$3=""; print $0}' | \
    sed 's/^ *//' | \
    sort | uniq -c | \
    sort -rn | head -5 | \
    awk '{$1=""; print "  -" $0}'
```
</details>

### Scenario 2: Pod Health Monitor (30 minutes)

**Task:**
Create a monitoring script that:
1. Checks all pods in a namespace
2. Reports pods with > 3 restarts
3. Reports pods not in Running state
4. Sends summary to stdout and log file
5. Exits with code 1 if any issues found

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

set -euo pipefail

NAMESPACE="${1:-default}"
LOGFILE="/var/log/pod-health.log"
RESTART_THRESHOLD=3

log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOGFILE"
}

log "Starting pod health check for namespace: $NAMESPACE"

issues_found=0

# Check for non-running pods
log "Checking for non-running pods..."
while read -r name status; do
    log "WARNING: Pod $name is $status"
    issues_found=$((issues_found + 1))
done < <(kubectl get pods -n "$NAMESPACE" --field-selector status.phase!=Running \
         -o custom-columns=NAME:.metadata.name,STATUS:.status.phase --no-headers 2>/dev/null || true)

# Check for high restart counts
log "Checking for pods with high restart counts..."
kubectl get pods -n "$NAMESPACE" -o json 2>/dev/null | \
    jq -r --arg thresh "$RESTART_THRESHOLD" '.items[] | 
           select(.status.containerStatuses[]?.restartCount > ($thresh | tonumber)) |
           "\(.metadata.name) \(.status.containerStatuses[0].restartCount)"' | \
    while read -r name restarts; do
        log "WARNING: Pod $name has $restarts restarts (threshold: $RESTART_THRESHOLD)"
        issues_found=$((issues_found + 1))
    done

# Summary
if [ $issues_found -eq 0 ]; then
    log "SUCCESS: All pods healthy"
    exit 0
else
    log "FAILED: Found $issues_found issue(s)"
    exit 1
fi
```
</details>

### Scenario 3: Disk Cleanup Automation (30 minutes)

**Task:**
Write a script that:
1. Checks disk usage of /var/log
2. If > 80%, deletes logs older than 7 days
3. If still > 80%, deletes logs older than 3 days
4. Logs all actions
5. Sends alert if still over 80% after cleanup

**Solution:**

<details>
<summary>Click for solution</summary>

```bash
#!/bin/bash

set -euo pipefail

LOGDIR="/var/log"
THRESHOLD=80
LOGFILE="/var/log/disk-cleanup.log"

log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOGFILE"
}

get_disk_usage() {
    df "$LOGDIR" | awk 'NR==2 {print $5}' | sed 's/%//'
}

log "Starting disk cleanup check"

usage=$(get_disk_usage)
log "Current disk usage: ${usage}%"

if [ "$usage" -le "$THRESHOLD" ]; then
    log "Disk usage OK, no cleanup needed"
    exit 0
fi

# First cleanup: > 7 days
log "Disk usage above threshold, cleaning logs > 7 days old"
find "$LOGDIR" -name "*.log" -type f -mtime +7 -delete 2>/dev/null
log "Deleted logs older than 7 days"

usage=$(get_disk_usage)
log "Disk usage after first cleanup: ${usage}%"

if [ "$usage" -le "$THRESHOLD" ]; then
    log "Disk usage now OK"
    exit 0
fi

# Second cleanup: > 3 days
log "Still above threshold, cleaning logs > 3 days old"
find "$LOGDIR" -name "*.log" -type f -mtime +3 -delete 2>/dev/null
log "Deleted logs older than 3 days"

usage=$(get_disk_usage)
log "Disk usage after second cleanup: ${usage}%"

if [ "$usage" -le "$THRESHOLD" ]; then
    log "Disk usage now OK"
    exit 0
else
    log "ALERT: Disk usage still at ${usage}% after cleanup!"
    exit 1
fi
```
</details>

---

## Module 10: Interview Tips and Common Pitfalls

**Time Required:** 30 minutes  
**Goal:** Learn what interviewers look for, avoid common mistakes

### What Interviewers Look For

1. **Problem understanding** - Ask clarifying questions
2. **Code organization** - Functions, clear variable names
3. **Error handling** - Check command success, validate input
4. **Efficiency** - Don't parse text multiple times unnecessarily
5. **Testing mindset** - Think about edge cases
6. **Communication** - Think out loud, explain your approach

### Common Mistakes to Avoid

**1. Not quoting variables:**
```bash
# WRONG - breaks with spaces
file=$1
cat $file

# RIGHT - always quote
file="$1"
cat "$file"
```

**2. Useless use of cat:**
```bash
# WRONG - unnecessary cat
cat file.txt | grep "ERROR"

# RIGHT - grep can read files
grep "ERROR" file.txt
```

**3. Ignoring exit codes:**
```bash
# WRONG - no error check
kubectl apply -f config.yaml
echo "Deployed successfully"

# RIGHT - check success
if kubectl apply -f config.yaml; then
    echo "Deployed successfully"
else
    echo "Deployment failed"
    exit 1
fi
```

**4. Not using `set -e`:**
```bash
# Add to top of scripts
set -euo pipefail

# This ensures:
# - Exit on error (-e)
# - Error on undefined variable (-u)
# - Pipe failures are caught (-o pipefail)
```

**5. Parsing `ls` output:**
```bash
# WRONG - breaks with special filenames
for file in $(ls *.log); do
    echo "$file"
done

# RIGHT - use glob
for file in *.log; do
    echo "$file"
done
```

### Interview Communication Tips

- **Think out loud:** "First I'll check if the file exists, then..."
- **Ask questions:** "Should I handle the case where the namespace doesn't exist?"
- **Explain trade-offs:** "I could use awk here, but sed is simpler for this task"
- **Admit uncertainty:** "I'm not sure about this syntax, let me look it up" (if allowed)
- **Test your code:** "Let me test this with an edge case"

---

## Module 11: Quick Reference Cheat Sheet

### Essential Commands

```bash
# File operations
cat, less, head, tail, grep, sed, awk, cut, sort, uniq, wc

# System info
df, du, ps, top, free, uptime, uname

# Networking
ping, curl, nc, nslookup, dig, ip, ss, lsof

# Kubernetes
kubectl get, describe, logs, exec, apply, delete, top

# Text processing pipeline example
grep "ERROR" app.log | awk '{print $5}' | sort | uniq -c | sort -rn
```

### Variable Patterns

```bash
${var:-default}     # Use default if unset
${var:=default}     # Set to default if unset
${var#prefix}       # Remove prefix
${var%suffix}       # Remove suffix
${#var}             # Length
```

### Common Loops

```bash
# For loop
for item in list; do
    echo "$item"
done

# While read (file processing)
while IFS= read -r line; do
    echo "$line"
done < file.txt

# While with command
while [ condition ]; do
    # commands
done
```

---

## Next Steps

**You've completed the Bash SRE Interview Prep guide!**

**What to do now:**

1. **Practice daily** - Run through exercises, write small scripts
2. **Build a toolkit** - Create your own collection of useful scripts
3. **Mock interviews** - Ask Claude to interview you (set 30 min timer!)
4. **Real scenarios** - Solve actual problems at work with Bash
5. **Combine with Python** - Know when to use Bash vs Python

**When to use Bash vs Python:**

**Use Bash when:**
- Quick one-liners, system administration
- Piping commands together
- File operations, text processing
- Simple automation

**Use Python when:**
- Complex logic, data structures
- API calls, JSON parsing
- Error handling needs to be sophisticated
- Code reusability across projects

**Good luck with your interviews!**
