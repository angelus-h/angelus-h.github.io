# Awk Programming Quick Reference

**Level:** Beginner to Advanced
**Prerequisites:** Basic command-line, text processing concepts

---

## Overview

Master `awk` - a powerful text processing language for data extraction, reporting, and transformation. Named after its creators: **A**ho, **W**einberger, and **K**ernighan.

**What You'll Learn:**
- Awk fundamentals and pattern-action paradigm
- Field and record processing
- Built-in variables and functions
- Arrays and associative arrays
- Control structures and user-defined functions
- Advanced text processing and reporting

---

## Week 1: Awk Fundamentals

### Module 1: Introduction to Awk

**Topics:**
- What is awk and when to use it
- Basic syntax and structure
- Pattern-action paradigm
- Records and fields

**Basic Syntax:**
```bash
# General format
awk 'pattern { action }' file.txt
awk -F'delimiter' 'program' file.txt
awk -f script.awk file.txt

# Simple examples
awk '{ print }' file.txt # Print all lines (like cat)
awk '{ print $0 }' file.txt # Same ($0 is entire line)
awk '{ print $1 }' file.txt # Print first field
awk '{ print $1, $3 }' file.txt # Print fields 1 and 3
```

**Fields and Records:**
```bash
# Default field separator is whitespace
echo "one two three" | awk '{ print $2 }'
# Output: two

# Custom field separator (-F)
echo "one:two:three" | awk -F':' '{ print $2 }'
# Output: two

# Multiple field separators
awk -F'[,:]' '{ print $1, $3 }' file.txt

# Tab-separated
awk -F'\t' '{ print $1 }' file.tsv

# Last field
awk '{ print $NF }' file.txt

# Second-to-last field
awk '{ print $(NF-1) }' file.txt
```

**Built-in Variables:**
```bash
# NR - Number of Records (line number)
awk '{ print NR, $0 }' file.txt

# NF - Number of Fields
awk '{ print NF, $0 }' file.txt

# FS - Field Separator (input)
awk 'BEGIN { FS=":" } { print $1 }' /etc/passwd

# OFS - Output Field Separator
awk 'BEGIN { OFS=" | " } { print $1, $2, $3 }' file.txt

# RS - Record Separator (default: newline)
awk 'BEGIN { RS=";" } { print }' file.txt

# FILENAME - Current filename
awk '{ print FILENAME, $0 }' file.txt
```

**Hands-On Practice:**
1. Extract specific columns from CSV files
2. Print line numbers with content
3. Display last field of each line in /etc/passwd
4. Count fields in each line of a file

### Module 2: Patterns and Actions

**Topics:**
- Pattern types (regex, comparison, range)
- BEGIN and END blocks
- Conditional patterns
- Logical operators

**Pattern Matching:**
```bash
# Regex pattern
awk '/error/ { print }' logfile.txt
awk '/^#/ { print }' file.txt # Lines starting with #
awk '/error|warning/ { print }' file.txt

# Comparison patterns
awk '$3 > 100 { print $1, $3 }' data.txt
awk '$2 == "active" { print $0 }' status.txt
awk 'NR > 5 && NR < 10 { print }' file.txt

# Field matching
awk '$1 ~ /^user/ { print $0 }' file.txt # Field 1 matches pattern
awk '$2 !~ /test/ { print $0 }' file.txt # Field 2 doesn't match

# Range patterns
awk '/START/,/END/ { print }' file.txt # Print between markers
awk 'NR==5,NR==10 { print }' file.txt # Lines 5-10
```

**BEGIN and END:**
```bash
# BEGIN: Execute before processing
awk 'BEGIN { print "Header" } { print $0 } END { print "Footer" }' file.txt

# Initialize variables
awk 'BEGIN { sum=0 } { sum+=$1 } END { print "Total:", sum }' numbers.txt

# Set separators
awk 'BEGIN { FS=":"; OFS="\t" } { print $1, $3 }' /etc/passwd

# Print header for report
awk 'BEGIN { print "Name\tAge\tCity" } { print $1, $2, $3 }' data.txt
```

**Logical Operators:**
```bash
# AND (&&)
awk '$3 > 50 && $4 < 100 { print }' file.txt

# OR (||)
awk '$1 == "ERROR" || $1 == "CRITICAL" { print }' log.txt

# NOT (!)
awk '!($2 ~ /test/) { print }' file.txt

# Ternary operator
awk '{ status = ($3 > 100) ? "high" : "low"; print $1, status }' file.txt
```

**Hands-On Practice:**
1. Filter log lines between two timestamps
2. Print lines where field 3 is numeric and > 50
3. Create a report with header and footer
4. Extract data between START and END markers

---

## Week 2: Data Processing

### Module 3: Arithmetic and String Operations

**Topics:**
- Arithmetic operations
- String functions
- String concatenation
- Type conversion

**Arithmetic:**
```bash
# Basic operations
awk '{ print $1 + $2 }' file.txt
awk '{ print $1 - $2 }' file.txt
awk '{ print $1 * $2 }' file.txt
awk '{ print $1 / $2 }' file.txt
awk '{ print $1 % $2 }' file.txt # Modulo
awk '{ print $1 ^ $2 }' file.txt # Exponent

# Increment/decrement
awk '{ count++; print count }' file.txt
awk '{ total += $3; print total }' file.txt

# Calculate average
awk '{ sum+=$1; count++ } END { print sum/count }' numbers.txt

# Statistics
awk '{ sum+=$1; sumsq+=$1*$1 } END { print "Avg:", sum/NR, "StdDev:", sqrt(sumsq/NR - (sum/NR)^2) }' data.txt
```

**String Functions:**
```bash
# length(str) - String length
awk '{ print length($1) }' file.txt

# substr(str, start, len) - Substring
awk '{ print substr($1, 1, 3) }' file.txt

# toupper(str), tolower(str)
awk '{ print toupper($1) }' file.txt
awk '{ print tolower($1) }' file.txt

# index(str, search) - Find substring position
awk '{ print index($1, "test") }' file.txt

# split(str, array, delimiter) - Split string
awk '{ split($0, arr, ":"); print arr[1] }' file.txt

# gsub(regex, replacement, target) - Global substitute
awk '{ gsub(/old/, "new"); print }' file.txt
awk '{ gsub(/old/, "new", $2); print }' file.txt

# sub(regex, replacement, target) - First substitute
awk '{ sub(/old/, "new"); print }' file.txt

# match(str, regex) - Test pattern
awk '{ if (match($0, /[0-9]+/)) print "Contains number" }' file.txt
```

**String Concatenation:**
```bash
# Space concatenation
awk '{ print $1 " " $2 }' file.txt

# Direct concatenation
awk '{ print $1 $2 }' file.txt

# Building strings
awk '{ name = $1 " " $2; print "Name:", name }' file.txt

# Format with printf
awk '{ printf "%s: %d\n", $1, $2 }' file.txt
```

**Hands-On Practice:**
1. Calculate sum and average of a column
2. Convert all text to uppercase
3. Extract file extensions using substr
4. Replace email domain in a list

### Module 4: Arrays and Associative Arrays

**Topics:**
- Indexed arrays
- Associative arrays (hash maps)
- Array operations
- Multi-dimensional arrays

**Indexed Arrays:**
```bash
# Create array
awk 'BEGIN {
 arr[1] = "apple"
 arr[2] = "banana"
 arr[3] = "orange"
 for (i=1; i<=3; i++) print arr[i]
}'

# Store fields in array
awk '{ for (i=1; i<=NF; i++) arr[i] = $i } END { for (i in arr) print arr[i] }' file.txt

# Array length
awk 'BEGIN { arr[1]="a"; arr[2]="b"; arr[3]="c"; print length(arr) }'
```

**Associative Arrays:**
```bash
# Count occurrences
awk '{ count[$1]++ } END { for (word in count) print word, count[word] }' file.txt

# Sum by category
awk '{ sum[$1] += $2 } END { for (cat in sum) print cat, sum[cat] }' data.txt

# Group by key
awk '{
 key = $1
 values[key] = values[key] " " $2
} END {
 for (k in values) print k, values[k]
}' file.txt

# Check if key exists
awk '{
 if ($1 in seen) {
 print "Duplicate:", $1
 } else {
 seen[$1] = 1
 }
}' file.txt
```

**Real-World Examples:**
```bash
# Count HTTP status codes
awk '{ status[$9]++ } END { for (code in status) print code, status[code] }' access.log

# Sum sales by product
awk -F',' '{ sales[$1] += $2 } END { for (prod in sales) print prod, sales[prod] }' sales.csv

# Find unique users
awk '!seen[$1]++ { print $1 }' users.txt

# Group errors by hour
awk '{
 match($0, /[0-9]{2}:[0-9]{2}/)
 hour = substr($0, RSTART, 2)
 errors[hour]++
} END {
 for (h in errors) print h ":00 -", errors[h], "errors"
}' error.log
```

**Multi-dimensional Arrays (simulated):**
```bash
# Use concatenation as key
awk '{
 arr[$1, $2] = $3
} END {
 for (key in arr) {
 split(key, indices, SUBSEP)
 print indices[1], indices[2], "=", arr[key]
 }
}' file.txt
```

**Hands-On Practice:**
1. Count word frequency in a text file
2. Calculate total sales per product from CSV
3. Find duplicate IP addresses in logs
4. Create a grade distribution histogram

---

## Week 3: Advanced Awk

### Module 5: Control Structures

**Topics:**
- if-else statements
- for loops
- while loops
- do-while loops
- break and continue

**If-Else:**
```bash
# Simple if
awk '{ if ($3 > 100) print $1, "HIGH" }' file.txt

# If-else
awk '{
 if ($3 > 100)
 print $1, "HIGH"
 else
 print $1, "LOW"
}' file.txt

# If-else-if
awk '{
 if ($3 > 100)
 status = "HIGH"
 else if ($3 > 50)
 status = "MEDIUM"
 else
 status = "LOW"
 print $1, status
}' file.txt
```

**For Loops:**
```bash
# Traditional for
awk 'BEGIN { for (i=1; i<=10; i++) print i }'

# Iterate fields
awk '{ for (i=1; i<=NF; i++) print $i }' file.txt

# Iterate array
awk '{
 for (i=1; i<=NF; i++) words[i] = $i
} END {
 for (i in words) print i, words[i]
}' file.txt

# Nested loops
awk 'BEGIN {
 for (i=1; i<=3; i++) {
 for (j=1; j<=3; j++) {
 print i, j
 }
 }
}'
```

**While Loops:**
```bash
# While loop
awk 'BEGIN {
 i = 1
 while (i <= 5) {
 print i
 i++
 }
}'

# Process until condition
awk '{
 i = 1
 while (i <= NF) {
 print $i
 i++
 }
}' file.txt

# Do-while
awk 'BEGIN {
 i = 1
 do {
 print i
 i++
 } while (i <= 5)
}'
```

**Break and Continue:**
```bash
# Break
awk '{
 for (i=1; i<=NF; i++) {
 if ($i == "STOP") break
 print $i
 }
}' file.txt

# Continue
awk '{
 for (i=1; i<=NF; i++) {
 if ($i ~ /^#/) continue
 print $i
 }
}' file.txt

# Next (skip to next record)
awk '/skip/ { next } { print }' file.txt
```

**Hands-On Practice:**
1. Create a multiplication table
2. Process only even-numbered lines
3. Skip lines containing specific patterns
4. Implement FizzBuzz in awk

### Module 6: Functions

**Topics:**
- Built-in functions
- User-defined functions
- Function parameters
- Return values

**Math Functions:**
```bash
# int(x) - Integer part
awk '{ print int($1) }' file.txt

# sqrt(x) - Square root
awk '{ print sqrt($1) }' file.txt

# exp(x), log(x)
awk '{ print exp($1), log($1) }' file.txt

# sin(x), cos(x), atan2(y,x)
awk 'BEGIN { print sin(3.14159/2) }'

# rand() - Random 0-1
awk 'BEGIN { print rand() }'

# srand([seed]) - Seed random
awk 'BEGIN { srand(); print rand() }'
```

**String Functions Review:**
```bash
# substr, length, toupper, tolower (covered earlier)
# gsub, sub, match, split (covered earlier)

# sprintf - Format string
awk '{ str = sprintf("%s: %05d", $1, $2); print str }' file.txt

# Additional functions
awk 'BEGIN {
 # Get system time
 print systime()

 # Format time
 print strftime("%Y-%m-%d", systime())
}'
```

**User-Defined Functions:**
```bash
# Basic function
awk '
function double(x) {
 return x * 2
}
{ print double($1) }
' file.txt

# Multiple parameters
awk '
function add(a, b) {
 return a + b
}
{ print add($1, $2) }
' file.txt

# Function with array
awk '
function sum_array(arr, size, i, total) {
 total = 0
 for (i=1; i<=size; i++)
 total += arr[i]
 return total
}
{
 for (i=1; i<=NF; i++)
 nums[i] = $i
 print sum_array(nums, NF)
}
' file.txt

# Void function (no return)
awk '
function print_header() {
 print "==== Report ===="
 print "Date:", strftime("%Y-%m-%d")
 print "================"
}
BEGIN { print_header() }
{ print $0 }
' file.txt
```

**Hands-On Practice:**
1. Create a temperature conversion function (C to F)
2. Write a factorial function
3. Build a string validation function library
4. Create statistical functions (mean, median, mode)

---

## Week 4: Real-World Applications

### Module 7: Log Analysis

**Apache/Nginx Access Logs:**
```bash
# Count requests by IP
awk '{ ip[$1]++ } END { for (i in ip) print i, ip[i] }' access.log

# Count HTTP status codes
awk '{ status[$9]++ } END { for (s in status) print s, status[s] }' access.log

# Sum bandwidth by status code
awk '{ bytes[$9] += $10 } END { for (s in bytes) print s, bytes[s]/1024/1024 "MB" }' access.log

# Top 10 URLs
awk '{ count[$7]++ } END { for (url in count) print count[url], url }' access.log | sort -rn | head -10

# Requests per hour
awk '{
 split($4, time, ":")
 hour = time[2]
 requests[hour]++
} END {
 for (h in requests) print h ":00 -", requests[h]
}' access.log | sort -n

# Average response time
awk '{ sum += $NF; count++ } END { print "Average:", sum/count "ms" }' access.log
```

**Application Logs:**
```bash
# Extract error messages
awk '/ERROR/ { print $1, $2, $NF }' app.log

# Count errors by type
awk '/ERROR/ {
 match($0, /ERROR: ([^,]+)/, err)
 errors[err[1]]++
} END {
 for (e in errors) print e, errors[e]
}' app.log

# Timeline of errors
awk '/ERROR/ {
 time = substr($1, 1, 5) # HH:MM
 timeline[time]++
} END {
 for (t in timeline) print t, timeline[t]
}' app.log | sort
```

**Hands-On Practice:**
1. Analyze nginx logs for top 20 IPs
2. Extract and count database query types
3. Build error rate chart from application logs
4. Create hourly traffic report

### Module 8: Data Transformation and Reporting

**CSV Processing:**
```bash
# Convert CSV to TSV
awk -F',' 'BEGIN { OFS="\t" } { $1=$1; print }' file.csv

# Extract specific columns
awk -F',' '{ print $1, $3, $5 }' file.csv

# Filter rows
awk -F',' '$3 > 1000 { print }' sales.csv

# Add calculated column
awk -F',' 'BEGIN { OFS="," } { print $0, $2*$3 }' file.csv

# Pivot data
awk -F',' '{
 sum[$1, $2] += $3
} END {
 for (key in sum) {
 split(key, k, SUBSEP)
 print k[1], k[2], sum[key]
 }
}' data.csv
```

**Report Generation:**
```bash
# Sales report
awk -F',' '
BEGIN {
 print "===== Sales Report ====="
 print "Product | Quantity | Revenue"
 print "--------|----------|--------"
}
{
 qty[$1] += $2
 revenue[$1] += $2 * $3
}
END {
 for (prod in qty) {
 printf "%-8s| %8d | $%7.2f\n", prod, qty[prod], revenue[prod]
 }
 print "======================="
}' sales.csv

# Formatted table
awk 'BEGIN { OFS="|" }
{
 printf "%-20s|%10s|%15s\n", $1, $2, $3
}' data.txt

# Multi-column report
awk '{
 col1[NR] = $1
 col2[NR] = $2
 col3[NR] = $3
} END {
 for (i=1; i<=NR; i++) {
 printf "%-15s %-15s %-15s\n", col1[i], col2[i], col3[i]
 }
}' file.txt
```

**Data Validation:**
```bash
# Validate CSV structure
awk -F',' 'NF != 5 { print "Line", NR, "has", NF, "fields" }' data.csv

# Check for empty fields
awk -F',' '{
 for (i=1; i<=NF; i++) {
 if ($i == "") print "Empty field at line", NR, "column", i
 }
}' data.csv

# Email validation
awk '{
 if ($1 !~ /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/) {
 print "Invalid email:", $1
 }
}' emails.txt

# Date format check
awk '{
 if ($1 !~ /^[0-9]{4}-[0-9]{2}-[0-9]{2}$/) {
 print "Invalid date format:", $1
 }
}' dates.txt
```

**Hands-On Practice:**
1. Create monthly sales summary from transaction log
2. Build data quality report for CSV files
3. Generate HTML table from CSV data
4. Create executive summary dashboard

---

## Advanced Topics

### Module 9: Awk Scripts

**Script File Structure:**
```awk
#!/usr/bin/awk -f
# report.awk - Sales report generator

BEGIN {
 FS = ","
 OFS = "\t"

 print "===== Sales Report ====="
 print "Generated:", strftime("%Y-%m-%d %H:%M:%S")
 print ""
}

# Skip header
NR == 1 { next }

# Process data
{
 product = $1
 quantity = $2
 price = $3

 total_qty[product] += quantity
 total_revenue[product] += quantity * price
}

END {
 print "Product\t\tQty\tRevenue"
 print "-------\t\t---\t-------"

 for (prod in total_qty) {
 printf "%-15s\t%d\t$%.2f\n",
 prod,
 total_qty[prod],
 total_revenue[prod]
 }
}
```

**Run script:**
```bash
chmod +x report.awk
./report.awk sales.csv
# Or
awk -f report.awk sales.csv
```

**Library Functions:**
```awk
# lib.awk - Reusable functions

function trim(str) {
 gsub(/^[ \t]+|[ \t]+$/, "", str)
 return str
}

function is_numeric(str) {
 return (str ~ /^[0-9]+$/)
}

function timestamp() {
 return strftime("%Y-%m-%d %H:%M:%S")
}

function log_message(level, msg) {
 print timestamp(), "[" level "]", msg
}

# Usage in another script:
# @include "lib.awk"
```

**Hands-On Practice:**
1. Create a reusable awk library
2. Build a complete ETL script
3. Write a data migration tool
4. Create automated report generator

### Module 10: Integration with Shell

**Awk in Shell Scripts:**
```bash
#!/bin/bash
# analyze.sh - Wrapper script

LOG_FILE="$1"

# Calculate statistics with awk
stats=$(awk '
 /ERROR/ { errors++ }
 /WARNING/ { warnings++ }
 END {
 print errors, warnings, NR
 }
' "$LOG_FILE")

read errors warnings total <<< "$stats"

echo "Total lines: $total"
echo "Errors: $errors"
echo "Warnings: $warnings"
echo "Error rate: $(awk "BEGIN { print ($errors/$total)*100 }")%"
```

**Combining with Other Tools:**
```bash
# Awk + grep
grep "ERROR" app.log | awk '{ print $1, $NF }'

# Awk + sort
awk '{ print $3, $1 }' file.txt | sort -n

# Awk + uniq
awk '{ print $1 }' file.txt | sort | uniq -c

# Pipeline example
cat access.log | \
 awk '{ print $1 }' | \
 sort | uniq -c | \
 sort -rn | \
 head -10
```

**Environment Variables:**
```bash
# Pass variables to awk
name="John"
awk -v user="$name" '{ print user, $0 }' file.txt

# Multiple variables
awk -v a="$VAR1" -v b="$VAR2" '{ print a, b, $0 }' file.txt

# Using ENVIRON
awk 'BEGIN { print ENVIRON["USER"], ENVIRON["HOME"] }'
```

**Hands-On Practice:**
1. Create a system monitoring script with awk
2. Build a log rotation tool
3. Write a backup verification script
4. Create a deployment automation tool

---

## Awk vs Sed vs Perl

**When to use Awk:**
- Column-based data processing
- Simple calculations and aggregations
- Quick data extraction and reporting
- Log file analysis

**When to use Sed:**
- Simple text substitutions
- Line-based editing
- Stream editing (no need for fields)

**When to use Perl/Python:**
- Complex data structures needed
- Need extensive libraries
- Multi-file complex operations
- When readability is priority

---

## Quick Reference

**Common Patterns:**
```bash
# Print specific fields
awk '{ print $1, $3 }'

# Sum column
awk '{ sum += $1 } END { print sum }'

# Average
awk '{ sum += $1 } END { print sum/NR }'

# Count occurrences
awk '{ count[$1]++ } END { for (i in count) print i, count[i] }'

# Unique values
awk '!seen[$1]++'

# Filter by condition
awk '$3 > 100'

# Custom separator
awk -F':' '{ print $1 }'

# Multiple separators
awk -F'[,:]' '{ print $1 }'

# Range of lines
awk 'NR>=10 && NR<=20'

# Substitute in field
awk '{ gsub(/old/, "new", $2); print }'
```

---

## Best Practices

1. **Use `-F` for field separator** instead of setting FS in BEGIN
2. **Quote your awk programs** to prevent shell expansion
3. **Use meaningful variable names** in complex scripts
4. **Comment complex logic** especially in script files
5. **Initialize variables** in BEGIN block
6. **Handle edge cases** (empty files, missing fields)
7. **Test incrementally** build complex programs step by step
8. **Use `-v`** for passing variables instead of quoting issues

---

## Resources

**Documentation:**
- GNU Awk manual: `info gawk`
- Man page: `man awk`
- Online: https://www.gnu.org/software/gawk/manual/

**Books:**
- "The AWK Programming Language" by Aho, Weinberger, Kernighan
- "sed & awk" by Dale Dougherty

**Online Resources:**
- Awk one-liners: https://www.pement.org/awk/awk1line.txt
- Awk.info: http://awk.info/

---

## Assessment Checklist

By the end of this learning plan, you should be able to:

- [ ] Process CSV and tabular data effectively
- [ ] Perform calculations and aggregations
- [ ] Use arrays and associative arrays
- [ ] Write user-defined functions
- [ ] Create formatted reports
- [ ] Analyze log files
- [ ] Combine awk with other Unix tools
- [ ] Write reusable awk scripts
- [ ] Debug awk programs
- [ ] Choose awk vs other tools appropriately
