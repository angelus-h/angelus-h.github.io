---
description: Python SRE interview prep - operational scripting, parsing, API calls, system automation, and live coding practice for SRE roles.
---

# Python for SRE - Interview Preparation

**Goal:** Prepare for live coding interviews for SRE positions 
**Time Required:** ~15-18 hours (2-3 weeks, 1 hour/day) 
**Level:** Beginner → Interview-ready 
**Focus:** Operational scripting, NOT general programming

---

## Why This Learning Plan?

### What this guide will NOT do:
- Deep algorithm and data structure theory 
- "Become a Python developer" approach 
- General programming concepts 
- LeetCode-style puzzles (though practice exercises are included)

### What THIS guide WILL do:
- **SRE-specific Python scripting** - operational automation 
- **Live coding confidence** - syntax fluency, speed 
- **Interview patterns** - working script in 30 minutes 
- **Thinking out loud** - communication practice 
- **Real-world SRE tasks** - health checks, log parsing, Kubernetes

### What this prepares you for:
- **Shopify-style live pair programming** (if you choose this path)
- **Technical screening coding exercises** (general SRE interviews)
- **Operational scripting questions** (real-world scenarios)
- **Mock interview practice** (Claude as interviewer)

---

## Learning Methodology

### Progressive Approach (3 phases):

**Week 1: Claude-Assisted Learning**
- Claude writes the solution (full solution)
- YOU read, understand, study it
- Rewrite it YOURSELF (without Claude, but you can look at the solution)
- Goal: **Syntax familiarity, pattern recognition**

**Week 2: Guided Practice**
- Task description
- YOU write it (Claude does NOT help initially!)
- If stuck → Claude hint (NOT full solution!)
- Testing, debugging
- Goal: **Independent problem-solving**

**Week 3: Timed Mock Interviews**
- Timer: 30 minutes / task
- Think out loud (as if in interview)
- No help (realistic interview conditions)
- Goal: **Interview simulation, pressure handling**

### Daily Routine (1 hour):
1. **Reading** (10 min) - module theory
2. **Example study** (10 min) - code comprehension
3. **Practice** (30 min) - task solving
4. **Review** (10 min) - what you learned, what was difficult

---

## Module 1: Python Basics for SRE

**Time Required:** ~1 hour 
**Goal:** Variables, types, control flow (if/for/while) - minimal theory, maximum practice

### 1.1 Variables and Basic Types

**What you need to know (minimal theory):**

```python
# String (text)
pod_name = "nginx-deployment-7d5c8f9b6d-abc12"
namespace = "production"

# Integer (number)
replica_count = 3
timeout_seconds = 30

# Float (decimal number)
cpu_usage = 0.75
memory_gb = 2.5

# Boolean (true/false)
is_healthy = True
has_errors = False

# List
namespaces = ["default", "kube-system", "production"]
pod_ips = ["10.0.1.5", "10.0.1.6", "10.0.1.7"]

# Dictionary (key-value pairs)
pod_status = {
"name": "nginx-abc12",
"status": "Running",
"restarts": 0
}
```

**Print and string formatting:**

```python
# Simple print
print("Pod name:", pod_name)

# f-string (MODERN, use this!)
print(f"Pod {pod_name} is in {namespace} namespace")
print(f"Replica count: {replica_count}")

# String concatenation (avoid, but works)
print("Namespace: " + namespace)
```

### 1.2 Control Flow (If/Else)

```python
# Simple if
if is_healthy:
print("Pod is healthy")

# If-else
if replica_count < 3:
print("WARNING: Low replica count")
else:
print("Replica count OK")

# If-elif-else
if cpu_usage > 0.9:
print("CRITICAL: High CPU usage")
elif cpu_usage > 0.7:
print("WARNING: Elevated CPU usage")
else:
print("CPU usage normal")

# Comparison operators
if timeout_seconds >= 30:
print("Timeout is sufficient")

if namespace == "production":
print("Production environment detected")

if namespace != "default":
print("Not using default namespace")
```

**SRE example - pod health check logic:**

```python
pod_status = "Running"
restart_count = 5

if pod_status != "Running":
health = "Unhealthy"
elif restart_count > 3:
health = "Degraded"
else:
health = "Healthy"

print(f"Pod health: {health}")
```

### 1.3 Loops (For and While)

**For loop - iterating lists:**

```python
# Simple list iteration
namespaces = ["default", "kube-system", "production"]

for ns in namespaces:
print(f"Namespace: {ns}")

# Dictionary iteration
pod_status = {
"name": "nginx-abc12",
"status": "Running",
"restarts": 0
}

for key, value in pod_status.items():
print(f"{key}: {value}")

# Range usage (counting)
for i in range(5): # 0, 1, 2, 3, 4
print(f"Replica {i}")

for i in range(1, 4): # 1, 2, 3
print(f"Attempt {i}")
```

**While loop (less common in SRE work):**

```python
# Retry logic example
attempts = 0
max_attempts = 3

while attempts < max_attempts:
print(f"Attempt {attempts + 1}")
attempts += 1
```

**SRE example - pod health summary:**

```python
pods = [
{"name": "nginx-1", "status": "Running"},
{"name": "nginx-2", "status": "Pending"},
{"name": "nginx-3", "status": "Running"},
]

healthy_count = 0
unhealthy_count = 0

for pod in pods:
if pod["status"] == "Running":
healthy_count += 1
else:
unhealthy_count += 1

print(f"Healthy: {healthy_count}, Unhealthy: {unhealthy_count}")
```

### 1.4 Practice Exercises (30 minutes)

**Exercise 1: Namespace Filter (EASY)**

```
Input: List of namespaces: ["default", "kube-system", "production", "staging"]
Task: 
- Iterate through the list
- Print only those that are NOT "default"
Output: kube-system, production, staging
```

**Solution (Week 1 - with Claude's help):**

<details>
<summary>Click for solution</summary>

```python
namespaces = ["default", "kube-system", "production", "staging"]

for ns in namespaces:
if ns != "default":
print(ns)
```
</details>

**Exercise 2: Pod Health Summary (EASY)**

```
Input: 
pods = [
{"name": "nginx-1", "status": "Running", "restarts": 0},
{"name": "nginx-2", "status": "Running", "restarts": 5},
{"name": "nginx-3", "status": "CrashLoopBackOff", "restarts": 10},
]

Task:
- Count how many pods have "Running" status
- Count how many pods have more than 3 restarts
- Print: "Running pods: X, High restart count: Y"
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
pods = [
{"name": "nginx-1", "status": "Running", "restarts": 0},
{"name": "nginx-2", "status": "Running", "restarts": 5},
{"name": "nginx-3", "status": "CrashLoopBackOff", "restarts": 10},
]

running_count = 0
high_restart_count = 0

for pod in pods:
if pod["status"] == "Running":
running_count += 1
if pod["restarts"] > 3:
high_restart_count += 1

print(f"Running pods: {running_count}, High restart count: {high_restart_count}")
```
</details>

**Exercise 3: CPU Alert Logic (MEDIUM)**

```
Input: cpu_usage = 0.85 (float, 0-1 range)

Task:
- If cpu_usage > 0.9 → print "CRITICAL: CPU usage X%"
- If cpu_usage > 0.7 → print "WARNING: CPU usage X%"
- Otherwise → print "OK: CPU usage X%"

Example output: "WARNING: CPU usage 85%"
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
cpu_usage = 0.85

percentage = int(cpu_usage * 100)

if cpu_usage > 0.9:
print(f"CRITICAL: CPU usage {percentage}%")
elif cpu_usage > 0.7:
print(f"WARNING: CPU usage {percentage}%")
else:
print(f"OK: CPU usage {percentage}%")
```
</details>

---

## Module 2: Functions and Error Handling

**Time Required:** ~1 hour 
**Goal:** Code reusability, try/except (CRITICAL interview skill!)

### 2.1 Function Basics

**Syntax:**

```python
# Simple function (no parameters)
def print_header():
print("=" * 50)
print("Pod Health Check Report")
print("=" * 50)

print_header()

# Function with parameter
def greet_namespace(namespace):
print(f"Checking namespace: {namespace}")

greet_namespace("production")

# Function with return value
def calculate_percentage(value, total):
percentage = (value / total) * 100
return percentage

result = calculate_percentage(75, 100)
print(f"Result: {result}%")

# Multiple return values
def get_pod_counts(pods):
running = 0
pending = 0
for pod in pods:
if pod["status"] == "Running":
running += 1
else:
pending += 1
return running, pending

running_count, pending_count = get_pod_counts(pods)
```

**SRE example - health check function:**

```python
def check_pod_health(pod):
"""
Check if a pod is healthy based on status and restart count.

Args:
pod: Dictionary with 'status' and 'restarts' keys

Returns:
String: "Healthy", "Degraded", or "Unhealthy"
"""
if pod["status"] != "Running":
return "Unhealthy"
elif pod["restarts"] > 3:
return "Degraded"
else:
return "Healthy"

# Usage
pod = {"name": "nginx-1", "status": "Running", "restarts": 5}
health = check_pod_health(pod)
print(f"Pod {pod['name']} is {health}")
```

### 2.2 Error Handling (try/except) - CRITICAL!

**Why it's important:** Live interviews ALWAYS ask about error handling!

**Basic try/except:**

```python
# Division by zero example
try:
result = 10 / 0
except ZeroDivisionError:
print("ERROR: Cannot divide by zero")

# File not found example
try:
with open("config.yaml", "r") as f:
content = f.read()
except FileNotFoundError:
print("ERROR: Config file not found")

# Generic exception (catch all)
try:
# Risky operation
result = some_operation()
except Exception as e:
print(f"ERROR: {str(e)}")
```

**SRE example - HTTP request with error handling:**

```python
import requests

def check_url_health(url, timeout=5):
"""
Check if URL is reachable and healthy.

Args:
url: URL to check
timeout: Request timeout in seconds

Returns:
Dictionary with status and message
"""
try:
response = requests.get(url, timeout=timeout)
if response.status_code == 200:
return {"status": "healthy", "message": "OK"}
else:
return {"status": "unhealthy", "message": f"HTTP {response.status_code}"}
except requests.exceptions.Timeout:
return {"status": "unhealthy", "message": "Timeout"}
except requests.exceptions.ConnectionError:
return {"status": "unhealthy", "message": "Connection failed"}
except Exception as e:
return {"status": "error", "message": str(e)}

# Usage
result = check_url_health("https://example.com")
print(f"Status: {result['status']}, Message: {result['message']}")
```

**Try/except/else/finally (complete structure):**

```python
try:
# Risky operation
with open("log.txt", "r") as f:
lines = f.readlines()
except FileNotFoundError:
print("ERROR: Log file not found")
lines = []
else:
# Only runs if NO exception occurred
print(f"Successfully read {len(lines)} lines")
finally:
# ALWAYS runs (cleanup)
print("Log read attempt completed")
```

### 2.3 Practice Exercises (30 minutes)

**Exercise 1: Temperature Converter (EASY)**

```
Write a function that converts Celsius to Fahrenheit.
Formula: F = (C * 9/5) + 32

def celsius_to_fahrenheit(celsius):
# Your code here
pass

# Test
print(celsius_to_fahrenheit(0)) # Should print 32.0
print(celsius_to_fahrenheit(100)) # Should print 212.0
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
def celsius_to_fahrenheit(celsius):
fahrenheit = (celsius * 9/5) + 32
return fahrenheit

print(celsius_to_fahrenheit(0)) # 32.0
print(celsius_to_fahrenheit(100)) # 212.0
```
</details>

**Exercise 2: Safe Division (MEDIUM)**

```
Write a function that divides safely (with error handling!).
If division by zero → return None and print error message.

def safe_divide(a, b):
# Your code here
pass

# Test
print(safe_divide(10, 2)) # Should print 5.0
print(safe_divide(10, 0)) # Should print error and return None
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
def safe_divide(a, b):
try:
result = a / b
return result
except ZeroDivisionError:
print("ERROR: Cannot divide by zero")
return None

print(safe_divide(10, 2)) # 5.0
print(safe_divide(10, 0)) # ERROR message, None
```
</details>

**Exercise 3: Pod Status Filter (MEDIUM)**

```
Write a function that filters pods by status from a list.

def filter_pods_by_status(pods, target_status):
# Your code here
pass

# Test
pods = [
{"name": "nginx-1", "status": "Running"},
{"name": "nginx-2", "status": "Pending"},
{"name": "nginx-3", "status": "Running"},
]

running_pods = filter_pods_by_status(pods, "Running")
print(running_pods) # Should print list with nginx-1 and nginx-3
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
def filter_pods_by_status(pods, target_status):
filtered = []
for pod in pods:
if pod["status"] == target_status:
filtered.append(pod)
return filtered

# Or list comprehension (advanced):
def filter_pods_by_status(pods, target_status):
return [pod for pod in pods if pod["status"] == target_status]

pods = [
{"name": "nginx-1", "status": "Running"},
{"name": "nginx-2", "status": "Pending"},
{"name": "nginx-3", "status": "Running"},
]

running_pods = filter_pods_by_status(pods, "Running")
print(running_pods)
```
</details>

---

## Module 3: Lists, Dictionaries, Sets

**Time Required:** ~1 hour 
**Goal:** SRE data structures - pod lists, config dicts, unique values

### 3.1 List Operations

```python
# List creation
pods = ["nginx-1", "nginx-2", "nginx-3"]
empty_list = []

# Adding
pods.append("nginx-4") # Add to end
pods.insert(0, "nginx-0") # Insert at index

# Removing
pods.remove("nginx-2") # Remove by value
last_pod = pods.pop() # Remove and return last
first_pod = pods.pop(0) # Remove by index

# Length and membership
count = len(pods) # Number of elements
if "nginx-1" in pods: # Check membership
print("Found nginx-1")

# Slicing (sub-list)
first_three = pods[0:3] # Index 0, 1, 2
last_two = pods[-2:] # Last two elements

# Sorting
pods.sort() # In-place sort
sorted_pods = sorted(pods) # New list (original unchanged)
```

**SRE example - pod name extraction:**

```python
# Pod names list (typically from Kubernetes API)
pod_names = [
"nginx-deployment-7d5c8f9b6d-abc12",
"redis-statefulset-0",
"nginx-deployment-7d5c8f9b6d-xyz34",
]

# Filter only nginx pods
nginx_pods = []
for name in pod_names:
if "nginx" in name:
nginx_pods.append(name)

print(f"Nginx pods: {nginx_pods}")

# List comprehension (advanced, but common in interviews!)
nginx_pods = [name for name in pod_names if "nginx" in name]
```

### 3.2 Dictionary Operations

```python
# Dictionary creation
pod = {
"name": "nginx-abc12",
"namespace": "production",
"status": "Running",
"restarts": 0
}

# Getting values
name = pod["name"] # If key missing → ERROR!
namespace = pod.get("namespace") # If key missing → None (safe)
namespace = pod.get("namespace", "default") # Default value

# Adding/modifying
pod["ip"] = "10.0.1.5" # New key-value
pod["restarts"] = 1 # Modification

# Deleting
del pod["ip"] # Delete key
removed_value = pod.pop("restarts", 0) # Pop with default

# Iteration
for key in pod: # Keys iteration
print(key)

for value in pod.values(): # Values iteration
print(value)

for key, value in pod.items(): # Key-value pairs
print(f"{key}: {value}")

# Membership
if "status" in pod:
print(f"Status: {pod['status']}")
```

**SRE example - config parsing:**

```python
# Kubernetes-style config
config = {
"metadata": {
"name": "nginx-deployment",
"namespace": "production"
},
"spec": {
"replicas": 3,
"template": {
"spec": {
"containers": [
{"name": "nginx", "image": "nginx:1.21"}
]
}
}
}
}

# Nested access
namespace = config["metadata"]["namespace"]
replicas = config["spec"]["replicas"]
image = config["spec"]["template"]["spec"]["containers"][0]["image"]

print(f"Namespace: {namespace}, Replicas: {replicas}, Image: {image}")

# Safe nested access
replicas = config.get("spec", {}).get("replicas", 1)
```

### 3.3 Sets (Unique Values)

```python
# Set creation
namespaces = {"production", "staging", "development"}
namespaces = set(["production", "staging", "production"]) # Duplicates removed

# Adding
namespaces.add("testing")

# Removing
namespaces.remove("staging") # If missing → ERROR!
namespaces.discard("staging") # If missing → OK (no error)

# Set operations
set1 = {"production", "staging", "development"}
set2 = {"production", "testing"}

union = set1 | set2 # Union: {'production', 'staging', 'development', 'testing'}
intersection = set1 & set2 # Intersection: {'production'}
difference = set1 - set2 # Difference: {'staging', 'development'}
```

**SRE example - unique namespace extraction:**

```python
pods = [
{"name": "nginx-1", "namespace": "production"},
{"name": "redis-1", "namespace": "production"},
{"name": "mysql-1", "namespace": "staging"},
{"name": "nginx-2", "namespace": "production"},
]

# Extract unique namespaces
namespaces = set()
for pod in pods:
namespaces.add(pod["namespace"])

print(f"Unique namespaces: {namespaces}")

# List comprehension + set
namespaces = {pod["namespace"] for pod in pods}
```

### 3.4 Practice Exercises (30 minutes)

**Exercise 1: Pod IP Collection (EASY)**

```
Input:
pods = [
{"name": "nginx-1", "ip": "10.0.1.5"},
{"name": "nginx-2", "ip": "10.0.1.6"},
{"name": "nginx-3", "ip": "10.0.1.7"},
]

Task: Collect all IPs into a list.
Output: ["10.0.1.5", "10.0.1.6", "10.0.1.7"]
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
pods = [
{"name": "nginx-1", "ip": "10.0.1.5"},
{"name": "nginx-2", "ip": "10.0.1.6"},
{"name": "nginx-3", "ip": "10.0.1.7"},
]

ips = []
for pod in pods:
ips.append(pod["ip"])

print(ips)

# Or list comprehension:
ips = [pod["ip"] for pod in pods]
```
</details>

**Exercise 2: Namespace Summary (MEDIUM)**

```
Input:
pods = [
{"name": "nginx-1", "namespace": "production"},
{"name": "redis-1", "namespace": "production"},
{"name": "mysql-1", "namespace": "staging"},
{"name": "nginx-2", "namespace": "production"},
{"name": "redis-2", "namespace": "staging"},
]

Task: Count how many pods per namespace.
Output (dictionary): {"production": 3, "staging": 2}
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
pods = [
{"name": "nginx-1", "namespace": "production"},
{"name": "redis-1", "namespace": "production"},
{"name": "mysql-1", "namespace": "staging"},
{"name": "nginx-2", "namespace": "production"},
{"name": "redis-2", "namespace": "staging"},
]

namespace_counts = {}

for pod in pods:
ns = pod["namespace"]
if ns in namespace_counts:
namespace_counts[ns] += 1
else:
namespace_counts[ns] = 1

print(namespace_counts)

# Or using .get() (more elegant):
namespace_counts = {}
for pod in pods:
ns = pod["namespace"]
namespace_counts[ns] = namespace_counts.get(ns, 0) + 1
```
</details>

**Exercise 3: Merge Pod Lists (HARD)**

```
Input: Two pod lists, merge them but keep ONLY unique pod names!

list1 = [
{"name": "nginx-1", "status": "Running"},
{"name": "nginx-2", "status": "Running"},
]

list2 = [
{"name": "nginx-2", "status": "Running"},
{"name": "nginx-3", "status": "Pending"},
]

Output: 3 unique pods (nginx-1, nginx-2, nginx-3)
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
list1 = [
{"name": "nginx-1", "status": "Running"},
{"name": "nginx-2", "status": "Running"},
]

list2 = [
{"name": "nginx-2", "status": "Running"},
{"name": "nginx-3", "status": "Pending"},
]

# Track seen names with set
seen_names = set()
merged = []

for pod in list1 + list2: # Concatenate lists
if pod["name"] not in seen_names:
merged.append(pod)
seen_names.add(pod["name"])

print(merged)
print(f"Total unique pods: {len(merged)}")
```
</details>

---

## Module 4: File Handling (Log Parsing, Config Files)

**Time Required:** ~1 hour 
**Goal:** File reading/writing, log parsing (CRITICAL SRE skill!)

### 4.1 Reading Files

**Basic file reading:**

```python
# Method 1: with statement (RECOMMENDED - auto close!)
with open("app.log", "r") as f:
content = f.read() # Entire file as string
print(content)

# Method 2: Read lines (list)
with open("app.log", "r") as f:
lines = f.readlines() # List, each line is an element
for line in lines:
print(line.strip()) # strip() removes newline

# Method 3: Iterate line by line (memory-efficient!)
with open("app.log", "r") as f:
for line in f:
print(line.strip())
```

**Error handling (ALWAYS!):**

```python
try:
with open("app.log", "r") as f:
content = f.read()
except FileNotFoundError:
print("ERROR: Log file not found")
content = ""
except PermissionError:
print("ERROR: Permission denied")
content = ""
```

**SRE example - error log parsing:**

```python
# Parse log file, count errors
def count_errors_in_log(log_file):
"""
Count lines containing 'ERROR' in log file.

Args:
log_file: Path to log file

Returns:
Integer count of error lines
"""
try:
error_count = 0
with open(log_file, "r") as f:
for line in f:
if "ERROR" in line:
error_count += 1
return error_count
except FileNotFoundError:
print(f"ERROR: {log_file} not found")
return 0

# Usage
count = count_errors_in_log("app.log")
print(f"Total errors: {count}")
```

### 4.2 Writing Files

```python
# Write to file (OVERWRITE!)
with open("output.txt", "w") as f:
f.write("First line\n")
f.write("Second line\n")

# Append to file
with open("output.txt", "a") as f:
f.write("Third line\n")

# Write list of lines
lines = ["Line 1\n", "Line 2\n", "Line 3\n"]
with open("output.txt", "w") as f:
f.writelines(lines)
```

**SRE example - error report generation:**

```python
def generate_error_report(log_file, output_file):
"""
Extract ERROR lines from log and write to report file.

Args:
log_file: Input log file path
output_file: Output report file path
"""
try:
errors = []
with open(log_file, "r") as f:
for line in f:
if "ERROR" in line:
errors.append(line)

with open(output_file, "w") as f:
f.write(f"Error Report - Total Errors: {len(errors)}\n")
f.write("=" * 50 + "\n")
for error in errors:
f.write(error)

print(f"Report generated: {output_file}")
except FileNotFoundError:
print(f"ERROR: {log_file} not found")
except Exception as e:
print(f"ERROR: {str(e)}")

# Usage
generate_error_report("app.log", "error_report.txt")
```

### 4.3 Advanced - Log Pattern Extraction

```python
# Extract specific patterns (e.g., timestamps, error codes)
import re

def extract_error_codes(log_file):
"""
Extract error codes like [E001], [E002] from log.

Args:
log_file: Path to log file

Returns:
List of unique error codes
"""
error_codes = set()
pattern = r'\[E\d+\]' # Matches [E001], [E123], etc.

try:
with open(log_file, "r") as f:
for line in f:
matches = re.findall(pattern, line)
for match in matches:
error_codes.add(match)
return list(error_codes)
except FileNotFoundError:
print(f"ERROR: {log_file} not found")
return []

# Usage
codes = extract_error_codes("app.log")
print(f"Found error codes: {codes}")
```

### 4.4 Practice Exercises (30 minutes)

**Exercise 1: Line Counter (EASY)**

```
Write a function that counts the number of lines in a file.

def count_lines(file_path):
# Your code here
pass

# Test (create test file first!)
with open("test.txt", "w") as f:
f.write("Line 1\nLine 2\nLine 3\n")

print(count_lines("test.txt")) # Should print 3
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
def count_lines(file_path):
try:
with open(file_path, "r") as f:
lines = f.readlines()
return len(lines)
except FileNotFoundError:
print(f"ERROR: {file_path} not found")
return 0

# Test
with open("test.txt", "w") as f:
f.write("Line 1\nLine 2\nLine 3\n")

print(count_lines("test.txt")) # 3
```
</details>

**Exercise 2: Warning Filter (MEDIUM)**

```
Write a function that filters lines containing "WARNING" from a log file
and returns them in a list.

def extract_warnings(log_file):
# Your code here
pass

# Test file:
with open("app.log", "w") as f:
f.write("INFO: Application started\n")
f.write("WARNING: Low disk space\n")
f.write("ERROR: Connection failed\n")
f.write("WARNING: High CPU usage\n")

warnings = extract_warnings("app.log")
print(warnings) # Should print 2 warning lines
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
def extract_warnings(log_file):
warnings = []
try:
with open(log_file, "r") as f:
for line in f:
if "WARNING" in line:
warnings.append(line.strip())
return warnings
except FileNotFoundError:
print(f"ERROR: {log_file} not found")
return []

# Test
with open("app.log", "w") as f:
f.write("INFO: Application started\n")
f.write("WARNING: Low disk space\n")
f.write("ERROR: Connection failed\n")
f.write("WARNING: High CPU usage\n")

warnings = extract_warnings("app.log")
print(warnings)
```
</details>

**Exercise 3: Log Summary Report (HARD)**

```
Write a function that analyzes a log file and creates a summary:
- How many INFO lines
- How many WARNING lines
- How many ERROR lines
- Write the result to a "summary.txt" file

def generate_log_summary(log_file, output_file):
# Your code here
pass

# Test
with open("app.log", "w") as f:
f.write("INFO: Application started\n")
f.write("WARNING: Low disk space\n")
f.write("ERROR: Connection failed\n")
f.write("INFO: Request processed\n")
f.write("WARNING: High CPU usage\n")
f.write("ERROR: Database timeout\n")

generate_log_summary("app.log", "summary.txt")
# Check summary.txt for output
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
def generate_log_summary(log_file, output_file):
counts = {"INFO": 0, "WARNING": 0, "ERROR": 0}

try:
# Count log levels
with open(log_file, "r") as f:
for line in f:
if "INFO" in line:
counts["INFO"] += 1
elif "WARNING" in line:
counts["WARNING"] += 1
elif "ERROR" in line:
counts["ERROR"] += 1

# Write summary
with open(output_file, "w") as f:
f.write("Log Summary Report\n")
f.write("=" * 30 + "\n")
for level, count in counts.items():
f.write(f"{level}: {count}\n")

print(f"Summary generated: {output_file}")
except FileNotFoundError:
print(f"ERROR: {log_file} not found")
except Exception as e:
print(f"ERROR: {str(e)}")

# Test
with open("app.log", "w") as f:
f.write("INFO: Application started\n")
f.write("WARNING: Low disk space\n")
f.write("ERROR: Connection failed\n")
f.write("INFO: Request processed\n")
f.write("WARNING: High CPU usage\n")
f.write("ERROR: Database timeout\n")

generate_log_summary("app.log", "summary.txt")

# Read result
with open("summary.txt", "r") as f:
print(f.read())
```
</details>

---

## Module 5: HTTP Requests (Health Checks, API Calls)

**Time Required:** ~1 hour 
**Goal:** Using requests library (CRITICAL SRE interview skill!)

### 5.1 Requests Library Basics

**Installation:**
```bash
pip install requests
```

**Basic GET request:**

```python
import requests

# Simple GET
response = requests.get("https://api.github.com")
print(response.status_code) # 200, 404, 500, etc.
print(response.text) # Response body (string)

# GET with timeout (ALWAYS use timeout!)
response = requests.get("https://api.github.com", timeout=5)

# JSON response parsing
response = requests.get("https://api.github.com/users/octocat")
data = response.json() # Parse JSON to dict
print(data["login"]) # octocat
```

**Error handling (CRITICAL!):**

```python
import requests

def safe_http_get(url, timeout=5):
"""
Safely perform HTTP GET with error handling.

Args:
url: URL to fetch
timeout: Request timeout in seconds

Returns:
Dictionary with status_code and content
"""
try:
response = requests.get(url, timeout=timeout)
return {
"success": True,
"status_code": response.status_code,
"content": response.text
}
except requests.exceptions.Timeout:
return {
"success": False,
"error": "Request timeout"
}
except requests.exceptions.ConnectionError:
return {
"success": False,
"error": "Connection failed"
}
except Exception as e:
return {
"success": False,
"error": str(e)
}

# Usage
result = safe_http_get("https://example.com")
if result["success"]:
print(f"Status: {result['status_code']}")
else:
print(f"Error: {result['error']}")
```

### 5.2 SRE Health Check Function

**Classic interview question: Check health of multiple URLs**

```python
import requests

def check_urls_health(urls, timeout=5):
"""
Check health status of multiple URLs.

Args:
urls: List of URLs to check
timeout: Request timeout in seconds

Returns:
Dictionary: {url: {"status": "healthy/unhealthy", "code": 200}}
"""
results = {}

for url in urls:
try:
response = requests.get(url, timeout=timeout)
if response.status_code == 200:
status = "healthy"
else:
status = "unhealthy"

results[url] = {
"status": status,
"code": response.status_code
}
except requests.exceptions.Timeout:
results[url] = {
"status": "unhealthy",
"error": "timeout"
}
except requests.exceptions.ConnectionError:
results[url] = {
"status": "unhealthy",
"error": "connection_failed"
}
except Exception as e:
results[url] = {
"status": "error",
"error": str(e)
}

return results

# Usage
urls = [
"https://google.com",
"https://github.com",
"https://invalid-domain-12345.com"
]

health_results = check_urls_health(urls)

for url, result in health_results.items():
print(f"{url}: {result['status']}")
```

### 5.3 POST Requests and Headers

```python
import requests

# POST with JSON body
data = {
"username": "admin",
"password": "secret"
}

response = requests.post(
"https://api.example.com/login",
json=data,
timeout=5
)

# Custom headers
headers = {
"Authorization": "Bearer token123",
"Content-Type": "application/json"
}

response = requests.get(
"https://api.example.com/users",
headers=headers,
timeout=5
)
```

### 5.4 Practice Exercises (30 minutes)

**Exercise 1: URL Checker (EASY)**

```
Write a function that checks if a URL is reachable (status code 200).
Return True if reachable, False if not.

def is_url_reachable(url, timeout=5):
# Your code here
pass

# Test
print(is_url_reachable("https://google.com")) # Should be True
print(is_url_reachable("https://invalid-url-12345.com")) # Should be False
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
import requests

def is_url_reachable(url, timeout=5):
try:
response = requests.get(url, timeout=timeout)
return response.status_code == 200
except:
return False

# Test
print(is_url_reachable("https://google.com")) # True
print(is_url_reachable("https://invalid-url-12345.com")) # False
```
</details>

**Exercise 2: Health Check Summary (MEDIUM)**

```
Write a function that checks a list of URLs and provides a summary:
- How many healthy (200 status)
- How many unhealthy (not 200, or error)

def summarize_health_check(urls, timeout=5):
# Your code here
# Return: {"healthy": count, "unhealthy": count}
pass

# Test
urls = [
"https://google.com",
"https://github.com",
"https://invalid-domain.com"
]
print(summarize_health_check(urls))
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
import requests

def summarize_health_check(urls, timeout=5):
healthy = 0
unhealthy = 0

for url in urls:
try:
response = requests.get(url, timeout=timeout)
if response.status_code == 200:
healthy += 1
else:
unhealthy += 1
except:
unhealthy += 1

return {"healthy": healthy, "unhealthy": unhealthy}

# Test
urls = [
"https://google.com",
"https://github.com",
"https://invalid-domain.com"
]
print(summarize_health_check(urls))
```
</details>

**Exercise 3: Service Monitor (HARD)**

```
Write a function that:
1. Checks a list of URLs
2. Prints the result to console ("URL: status")
3. Writes a full report to "health_report.txt" file

def monitor_services(urls, output_file, timeout=5):
# Your code here
pass

# Test
urls = [
"https://google.com",
"https://github.com",
"https://stackoverflow.com"
]

monitor_services(urls, "health_report.txt")
# Check console output and health_report.txt file
```

**Solution:**

<details>
<summary>Click for solution</summary>

```python
import requests

def monitor_services(urls, output_file, timeout=5):
results = []

# Check all URLs
for url in urls:
try:
response = requests.get(url, timeout=timeout)
if response.status_code == 200:
status = "healthy"
else:
status = f"unhealthy (HTTP {response.status_code})"
except requests.exceptions.Timeout:
status = "unhealthy (timeout)"
except requests.exceptions.ConnectionError:
status = "unhealthy (connection failed)"
except Exception as e:
status = f"error ({str(e)})"

# Print to console
print(f"{url}: {status}")

# Store for file output
results.append(f"{url}: {status}\n")

# Write to file
try:
with open(output_file, "w") as f:
f.write("Service Health Report\n")
f.write("=" * 50 + "\n")
for line in results:
f.write(line)
print(f"\nReport saved to {output_file}")
except Exception as e:
print(f"ERROR writing report: {str(e)}")

# Test
urls = [
"https://google.com",
"https://github.com",
"https://stackoverflow.com"
]

monitor_services(urls, "health_report.txt")
```
</details>

---

## Modules 6-12: Additional Modules (Continued)

**IMPORTANT:** This learning plan is LARGE (~35-40K words in full form)!

**Next modules (brief summary):**

### Module 6: Kubernetes Python Client
- Installing `kubernetes` library
- Listing pods, querying status
- Namespace enumeration
- Error handling for K8s API calls

### Module 7: JSON/YAML Parsing
- `json` module (built-in)
- `yaml` library (PyYAML)
- Config file parsing
- Kubernetes manifest parsing

### Module 8: Command Line Arguments
- `argparse` module
- Script parameterization
- Environment variables (`os.environ`)

### Module 9: Regular Expressions
- `re` module
- Pattern matching
- Log parsing patterns
- Error code extraction

### Module 10: String Manipulation
- `.split()`, `.join()`, `.strip()`
- `.replace()`, `.format()`
- String slicing
- Case conversion

### Module 11: Timed Practice Exercises (30 min challenges)
- 10 interview-style tasks
- Timer: 30 min / task
- Progressive difficulty
- Realistic scenarios

### Module 12: Mock Interview Preparation
- Full interview simulation (90 min)
- Claude as interviewer
- Live coding setup (VSCode screen share)
- Feedback and improvements

---

## Quick Win: 1 Week Practice Tasks

**If you only have 1 week (5-7 hours total):**

**Day 1-2: Modules 1-3** (basics, functions, data structures) 
**Day 3-4: Modules 4-5** (file ops, HTTP requests) 
**Day 5-7: Practice exercises** (timed challenges)

**This is MINIMAL preparation, but provides a baseline!**

---

## Next Steps

**WHAT DO YOU WANT NOW?**

**Option 1:** **Continue the full learning plan** (Modules 6-12 in detail)
- ~25-30K words remaining
- Complete interview prep (2-3 weeks)
- All SRE Python skills covered

**Option 2:** **Quick Reference cheat sheet first** 
- 1-2 pages, quick lookup
- Syntax patterns
- Interview pitfalls
- Then full learning plan later

**Option 3:** **Start practice NOW** (based on Modules 1-5)
- Complete all exercises
- Come back with questions
- Continue Modules 6-12 later

**Tell me which direction!**
