# Python Gyors Referencia - SRE Interview Cheat Sheet

**Cél:** Gyors lookup syntax-hoz és gyakori patterns-ekhez  
**Használat:** Tartsd nyitva practice közben, interview előtt átfutni  
**Fókusz:** SRE operational scripting (NEM general programming)

---

## 1. Alapvető Syntax

### Változók és Típusok
```python
# String
pod_name = "nginx-abc12"
namespace = 'production'  # Single vagy double quotes OK

# Integer
replica_count = 3

# Float
cpu_usage = 0.75

# Boolean
is_healthy = True  # Capitalize!

# None (null)
error_message = None
```

### Print és String Formatting
```python
# F-string (MODERN - használd ezt!)
print(f"Pod {pod_name} in {namespace}")
print(f"CPU: {cpu_usage * 100}%")

# .format() (régi, de működik)
print("Pod {} in {}".format(pod_name, namespace))

# % formatting (LEGACY - kerüld!)
print("Pod %s in %s" % (pod_name, namespace))
```

### Kommentek
```python
# Single line comment

"""
Multi-line comment
(vagy docstring)
"""
```

---

## 2. Control Flow

### If/Elif/Else
```python
# Basic if
if condition:
    do_something()

# If-else
if cpu_usage > 0.9:
    print("CRITICAL")
else:
    print("OK")

# If-elif-else
if cpu_usage > 0.9:
    status = "CRITICAL"
elif cpu_usage > 0.7:
    status = "WARNING"
else:
    status = "OK"

# One-liner (ternary)
status = "healthy" if is_running else "unhealthy"
```

### Comparison Operators
```python
==  # Equal
!=  # Not equal
>   # Greater than
<   # Less than
>=  # Greater than or equal
<=  # Less than or equal

# Logical operators
and  # Both true
or   # At least one true
not  # Negation

# Membership
if "nginx" in pod_name:
    print("Found nginx")
```

### Loops
```python
# For loop (list)
for item in list:
    print(item)

# For loop (range)
for i in range(5):  # 0, 1, 2, 3, 4
    print(i)

for i in range(1, 4):  # 1, 2, 3
    print(i)

# For loop (dict)
for key, value in dictionary.items():
    print(f"{key}: {value}")

# While loop
while condition:
    do_something()

# Break and continue
for item in items:
    if item == "skip":
        continue  # Skip this iteration
    if item == "stop":
        break  # Exit loop
    process(item)
```

---

## 3. Data Structures

### Lists
```python
# Create
pods = ["nginx-1", "nginx-2"]
empty = []

# Access
first = pods[0]
last = pods[-1]

# Add
pods.append("nginx-3")  # End
pods.insert(0, "nginx-0")  # Index

# Remove
pods.remove("nginx-2")  # By value
last = pods.pop()  # Remove & return last
first = pods.pop(0)  # Remove & return at index

# Length
count = len(pods)

# Membership
if "nginx-1" in pods:
    print("Found")

# Slicing
first_three = pods[0:3]
last_two = pods[-2:]

# Sorting
pods.sort()  # In-place
sorted_pods = sorted(pods)  # New list

# List comprehension (COMMON in interviews!)
nginx_pods = [p for p in pods if "nginx" in p]
```

### Dictionaries
```python
# Create
pod = {"name": "nginx-1", "status": "Running"}
empty = {}

# Access
name = pod["name"]  # KeyError if missing!
name = pod.get("name")  # None if missing
name = pod.get("name", "default")  # Default value

# Add/Modify
pod["ip"] = "10.0.1.5"
pod["status"] = "Pending"

# Remove
del pod["ip"]
value = pod.pop("status", "unknown")  # Pop with default

# Keys, values, items
keys = pod.keys()
values = pod.values()
items = pod.items()  # (key, value) tuples

# Iteration
for key in pod:
    print(key)

for key, value in pod.items():
    print(f"{key}: {value}")

# Membership
if "status" in pod:
    print(pod["status"])

# Nested access
namespace = config["metadata"]["namespace"]
# Safe nested:
namespace = config.get("metadata", {}).get("namespace", "default")
```

### Sets
```python
# Create
namespaces = {"prod", "staging", "dev"}
namespaces = set(["prod", "staging"])

# Add/Remove
namespaces.add("testing")
namespaces.remove("staging")  # KeyError if missing!
namespaces.discard("staging")  # No error if missing

# Set operations
union = set1 | set2
intersection = set1 & set2
difference = set1 - set2

# Set comprehension
namespaces = {pod["namespace"] for pod in pods}
```

---

## 4. Functions

### Basic Function
```python
def function_name(param1, param2):
    """Docstring (optional but recommended)"""
    result = param1 + param2
    return result

# Call
output = function_name(10, 20)
```

### Default Arguments
```python
def check_health(url, timeout=5):
    # timeout default is 5
    pass

check_health("http://example.com")  # Uses timeout=5
check_health("http://example.com", 10)  # Uses timeout=10
```

### Multiple Return Values
```python
def get_counts(items):
    count_a = items.count("A")
    count_b = items.count("B")
    return count_a, count_b

a, b = get_counts(["A", "B", "A"])
```

### Lambda (Anonymous Function)
```python
# Rarely needed in SRE interviews, but good to know
square = lambda x: x ** 2
result = square(5)  # 25

# Common use: sorting with key
pods.sort(key=lambda p: p["name"])
```

---

## 5. Error Handling

### Try/Except (CRITICAL!)
```python
# Basic
try:
    risky_operation()
except Exception as e:
    print(f"ERROR: {str(e)}")

# Specific exceptions
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")
except ValueError:
    print("Invalid value")

# Try/except/else/finally
try:
    risky_operation()
except Exception as e:
    print(f"ERROR: {e}")
else:
    print("Success!")  # Only if NO exception
finally:
    print("Cleanup")  # ALWAYS runs
```

### Common Exceptions (SRE context)
```python
FileNotFoundError  # File not found
PermissionError  # Permission denied
ValueError  # Invalid value
KeyError  # Dict key missing
IndexError  # List index out of range
ZeroDivisionError  # Division by zero
requests.exceptions.Timeout  # HTTP timeout
requests.exceptions.ConnectionError  # HTTP connection failed
```

---

## 6. File Operations

### Reading Files
```python
# Read all (small files)
with open("file.txt", "r") as f:
    content = f.read()

# Read lines (list)
with open("file.txt", "r") as f:
    lines = f.readlines()

# Iterate (memory-efficient for large files)
with open("file.txt", "r") as f:
    for line in f:
        print(line.strip())

# Error handling (ALWAYS!)
try:
    with open("file.txt", "r") as f:
        content = f.read()
except FileNotFoundError:
    print("File not found")
```

### Writing Files
```python
# Write (overwrite)
with open("file.txt", "w") as f:
    f.write("First line\n")
    f.write("Second line\n")

# Append
with open("file.txt", "a") as f:
    f.write("New line\n")

# Write list of lines
lines = ["Line 1\n", "Line 2\n"]
with open("file.txt", "w") as f:
    f.writelines(lines)
```

---

## 7. HTTP Requests (requests library)

### Basic GET
```python
import requests

# Simple GET
response = requests.get("https://example.com")
print(response.status_code)  # 200, 404, etc.
print(response.text)  # Body as string
data = response.json()  # Parse JSON to dict

# With timeout (ALWAYS use!)
response = requests.get("https://example.com", timeout=5)
```

### Error Handling (CRITICAL!)
```python
import requests

try:
    response = requests.get(url, timeout=5)
    if response.status_code == 200:
        print("Healthy")
    else:
        print(f"Unhealthy: {response.status_code}")
except requests.exceptions.Timeout:
    print("Timeout")
except requests.exceptions.ConnectionError:
    print("Connection failed")
except Exception as e:
    print(f"Error: {str(e)}")
```

### POST Request
```python
import requests

data = {"key": "value"}
response = requests.post(
    "https://api.example.com/endpoint",
    json=data,
    timeout=5
)
```

### Headers
```python
headers = {
    "Authorization": "Bearer token123",
    "Content-Type": "application/json"
}
response = requests.get(url, headers=headers, timeout=5)
```

---

## 8. String Operations

### Common Methods
```python
# Case
text.upper()  # "HELLO"
text.lower()  # "hello"
text.capitalize()  # "Hello"

# Strip whitespace
text.strip()  # Remove leading/trailing
text.lstrip()  # Remove leading
text.rstrip()  # Remove trailing

# Split/Join
parts = text.split(",")  # Split by comma
text = ",".join(parts)  # Join with comma

# Replace
new_text = text.replace("old", "new")

# Startswith/Endswith
if text.startswith("ERROR"):
    print("Error line")
if text.endswith(".log"):
    print("Log file")

# Contains
if "ERROR" in text:
    print("Found error")
```

---

## 9. JSON/YAML Parsing

### JSON (built-in)
```python
import json

# Parse JSON string
json_string = '{"name": "nginx", "status": "Running"}'
data = json.loads(json_string)
print(data["name"])

# Parse JSON file
with open("config.json", "r") as f:
    config = json.load(f)

# Write JSON
data = {"name": "nginx", "replicas": 3}
json_string = json.dumps(data)  # To string
json_string = json.dumps(data, indent=2)  # Pretty print

with open("output.json", "w") as f:
    json.dump(data, f, indent=2)
```

### YAML (requires PyYAML)
```python
import yaml

# Parse YAML file
with open("config.yaml", "r") as f:
    config = yaml.safe_load(f)

# Write YAML
data = {"name": "nginx", "replicas": 3}
with open("output.yaml", "w") as f:
    yaml.dump(data, f)
```

---

## 10. Common Patterns (SRE Interview)

### Health Check Function
```python
import requests

def check_url_health(url, timeout=5):
    try:
        response = requests.get(url, timeout=timeout)
        return {
            "url": url,
            "status": "healthy" if response.status_code == 200 else "unhealthy",
            "code": response.status_code
        }
    except requests.exceptions.Timeout:
        return {"url": url, "status": "timeout"}
    except requests.exceptions.ConnectionError:
        return {"url": url, "status": "connection_failed"}
    except Exception as e:
        return {"url": url, "status": "error", "message": str(e)}
```

### Log Error Counter
```python
def count_errors_in_log(log_file):
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
```

### Pod Status Summary
```python
def summarize_pod_status(pods):
    counts = {}
    for pod in pods:
        status = pod.get("status", "unknown")
        counts[status] = counts.get(status, 0) + 1
    return counts

# Usage
pods = [
    {"name": "nginx-1", "status": "Running"},
    {"name": "nginx-2", "status": "Pending"},
    {"name": "nginx-3", "status": "Running"},
]
summary = summarize_pod_status(pods)
# {"Running": 2, "Pending": 1}
```

### Unique Namespace Extraction
```python
def get_unique_namespaces(pods):
    namespaces = set()
    for pod in pods:
        namespaces.add(pod["namespace"])
    return list(namespaces)

# Or one-liner with set comprehension:
def get_unique_namespaces(pods):
    return list({pod["namespace"] for pod in pods})
```

---

## 11. Interview Pitfalls (GYAKORI HIBÁK!)

### ❌ Hiba 1: Dictionary Key Access Crashel
```python
# BAD - crashes if key missing!
name = pod["name"]

# GOOD - safe with default
name = pod.get("name", "unknown")
```

### ❌ Hiba 2: File Close Nélkül
```python
# BAD - file not closed if error!
f = open("file.txt", "r")
content = f.read()
f.close()

# GOOD - auto close with 'with'
with open("file.txt", "r") as f:
    content = f.read()
```

### ❌ Hiba 3: HTTP Timeout Hiányzik
```python
# BAD - can hang forever!
response = requests.get(url)

# GOOD - timeout always!
response = requests.get(url, timeout=5)
```

### ❌ Hiba 4: Error Handling Nincs
```python
# BAD - crashes on error
result = 10 / x

# GOOD - error handling
try:
    result = 10 / x
except ZeroDivisionError:
    print("Cannot divide by zero")
    result = None
```

### ❌ Hiba 5: List Modification During Iteration
```python
# BAD - modifying list while iterating!
for item in items:
    if condition:
        items.remove(item)  # Can skip items!

# GOOD - iterate on copy
for item in items[:]:
    if condition:
        items.remove(item)

# Or list comprehension:
items = [item for item in items if not condition]
```

### ❌ Hiba 6: Indentation Error
```python
# Python REQUIRES consistent indentation!
def my_function():
    if condition:
        do_something()  # 4 spaces
      do_other()  # ERROR: inconsistent indent!
```

---

## 12. Interview Communication Tips

### Gondolkodj Hangosan!
```
❌ BAD: *Csendben kódol 5 percig*

✅ GOOD:
"OK, so I need to check the health of multiple URLs.
First, I'll create a function that takes a list of URLs.
Then I'll iterate through them with a for loop.
For each URL, I'll use requests.get with a timeout.
I need error handling for timeout and connection errors.
Let me start with the function definition..."
```

### Ha Elakadsz - Kommunikálj!
```
✅ "I'm thinking about the best way to handle this error case.
   Should I return None or raise an exception here?"

✅ "I know there's a more elegant way to do this with list comprehension,
   but let me write the for loop version first to make sure the logic is correct."

✅ "For the timeout value, I'd normally make this configurable,
   but I'll hardcode 5 seconds for now."
```

### Syntax Uncertainty - Acknowledge It!
```
✅ "I believe the syntax for splitting a string is .split(),
   let me verify... yes, that's correct."

✅ "I want to make sure I get the exact syntax right for this dict iteration.
   I think it's .items(), which gives me key-value pairs."

❌ "Umm... I think... maybe... it's like... something.split()?"
   (Don't sound uncertain - acknowledge and verify!)
```

---

## 13. Live Coding Setup Checklist

**Interview előtt (10 perc):**
- [ ] VSCode / PyCharm megnyitva
- [ ] Terminal ready (Python interpreter works)
- [ ] requests library installed (`pip install requests`)
- [ ] Screen share tested (látszik a kód?)
- [ ] Audio/video works
- [ ] Close distracting applications
- [ ] Have water nearby
- [ ] Toilet break előtte!

**Coding közben:**
- [ ] Gondolkodj hangosan (explain your approach)
- [ ] Ask clarifying questions ("Should I handle this edge case?")
- [ ] Test your code (run it!)
- [ ] Acknowledge syntax uncertainty (don't guess silently)
- [ ] Keep calm (pressure is normal!)

---

## 14. Quick Reference Card (Zsebre Való!)

```python
# Lists
list = ["a", "b", "c"]
list.append("d")
list.pop()
len(list)
"a" in list

# Dicts
d = {"key": "value"}
d["key"]
d.get("key", "default")
d.items()
"key" in d

# Loops
for item in list:
    print(item)

for k, v in dict.items():
    print(k, v)

# Functions
def func(param, default=5):
    return result

# Files
with open("f.txt", "r") as f:
    content = f.read()

# HTTP
import requests
r = requests.get(url, timeout=5)
r.status_code
r.json()

# Error handling
try:
    risky()
except Exception as e:
    print(str(e))
```

---

## 15. Practice Checklist (Before Interview)

**Week 1-2:**
- [ ] Modul 1-5 gyakorló feladatok megoldva
- [ ] Minden pattern érted (lists, dicts, functions, files, HTTP)
- [ ] Error handling muscle memory (try/except automatikus)

**Week 2-3:**
- [ ] Timed practice (30 perc / feladat, 5-10 feladat)
- [ ] Mock interview (Claude mint interviewer, 1-2 alkalom)
- [ ] Syntax fluency (nem kell Google-ezni basic dolgokat)

**Interview előtt (1 nap):**
- [ ] Olvasd át ezt a cheat sheet-et
- [ ] Gyakorolj 1-2 egyszerű feladatot (warm-up)
- [ ] Setup checklist (VSCode, screen share, etc.)
- [ ] Aludj jól!

---

## Végszó

**TE TUDOD CSINÁLNI!**

Ez NEM rocket science - ezek **patterns**, amik gyakorlással memóriába égnek.

**1 hét practice = baseline confidence**  
**2 hét practice = interview-ready**  
**3 hét practice = strong performance**

**Legfontosabb:**
- Gondolkodj hangosan
- Error handling MINDIG
- Timeout MINDIG (HTTP requests)
- Try/except for file operations
- .get() for dict access (safe)

**Ha elakadsz:** Acknowledge, explain your thinking, ask clarifying questions.

**Hajrá! 🚀**
