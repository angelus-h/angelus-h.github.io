# Sed (Stream Editor) Quick Reference

**Level:** Beginner to Advanced
**Prerequisites:** Basic command-line and regular expressions

---

## Overview

Master `sed` (Stream Editor) for powerful text manipulation, log processing, and automated editing in Unix/Linux environments.

**What You'll Learn:**
- Stream editing fundamentals
- Search and replace patterns
- Regular expressions in sed
- Multi-line editing
- In-place file editing
- Sed scripting and automation

---

## Week 1: Sed Fundamentals

### Module 1: Introduction to Sed

**Topics:**
- What is sed and why use it
- Basic syntax and command structure
- Pattern space and hold space
- Common use cases

**Basic Syntax:**
```bash
# General format
sed 'command' file.txt
sed -e 'command1' -e 'command2' file.txt
sed -f script.sed file.txt

# Print (p command)
sed 'p' file.txt              # Print every line twice
sed -n 'p' file.txt           # Print every line once (-n suppresses default)

# Specific line
sed -n '3p' file.txt          # Print line 3
sed -n '1,5p' file.txt        # Print lines 1-5
sed -n '10,$p' file.txt       # Print line 10 to end

# Pattern matching
sed -n '/error/p' file.txt    # Print lines containing "error"
sed -n '/^#/p' file.txt       # Print comment lines
```

**Delete (d command):**
```bash
# Delete lines
sed '3d' file.txt             # Delete line 3
sed '1,5d' file.txt           # Delete lines 1-5
sed '/pattern/d' file.txt     # Delete lines matching pattern
sed '/^$/d' file.txt          # Delete empty lines
sed '/^#/d' file.txt          # Delete comment lines

# Delete except pattern
sed -n '/pattern/!d; p' file.txt
```

**Hands-On Practice:**
1. Extract specific lines from a log file
2. Remove all comment lines from a config file
3. Print only error messages from logs
4. Delete empty lines from multiple files

### Module 2: Search and Replace

**Topics:**
- Substitution command (s)
- Flags (g, i, p)
- Delimiters
- Back-references

**Basic Substitution:**
```bash
# Replace first occurrence on each line
sed 's/old/new/' file.txt

# Replace all occurrences (g flag)
sed 's/old/new/g' file.txt

# Replace on specific line
sed '3s/old/new/' file.txt
sed '1,10s/old/new/g' file.txt

# Case-insensitive (I flag)
sed 's/error/ERROR/I' file.txt

# Print only changed lines (p flag with -n)
sed -n 's/old/new/p' file.txt

# Combine flags
sed 's/error/ERROR/gip' file.txt
```

**Alternative Delimiters:**
```bash
# Use different delimiter when pattern contains /
sed 's/\/path\/to\/old/\/path\/to\/new/' file.txt  # Hard to read

# Better with different delimiter
sed 's|/path/to/old|/path/to/new|' file.txt
sed 's#/path/to/old#/path/to/new#' file.txt
sed 's@http://old@https://new@' file.txt
```

**Back-references:**
```bash
# Capture groups with \( \) and reference with \1, \2, etc.
sed 's/\([0-9]*\)-\([0-9]*\)/\2-\1/' file.txt  # Swap numbers

# Example: 123-456 becomes 456-123

# Extract parts
echo "John Doe" | sed 's/\(.*\) \(.*\)/Last: \2, First: \1/'
# Output: Last: Doe, First: John

# Duplicate pattern
sed 's/\(word\)/\1 \1/' file.txt  # "word" becomes "word word"

# Phone number formatting
echo "5551234567" | sed 's/\([0-9]\{3\}\)\([0-9]\{3\}\)\([0-9]\{4\}\)/(\1) \2-\3/'
# Output: (555) 123-4567
```

**Hands-On Practice:**
1. Replace all IP addresses in a config file
2. Convert dates from MM/DD/YYYY to YYYY-MM-DD
3. Add quotes around all email addresses
4. Convert URLs from HTTP to HTTPS

---

## Week 2: Advanced Sed Techniques

### Module 3: Regular Expressions in Sed

**Topics:**
- Character classes
- Quantifiers
- Anchors
- Extended regex (-E or -r)

**Character Classes:**
```bash
# Match digits
sed 's/[0-9]/X/g' file.txt

# Match letters
sed 's/[a-zA-Z]/*/g' file.txt

# Match whitespace
sed 's/[[:space:]]//' file.txt

# Negation
sed 's/[^0-9]//g' file.txt    # Remove all non-digits
```

**Quantifiers:**
```bash
# * (zero or more)
sed 's/a*/X/' file.txt

# \+ (one or more) - requires escaping
sed 's/[0-9]\+/NUM/g' file.txt

# \? (zero or one)
sed 's/colou\?r/color/g' file.txt  # Matches color and colour

# \{n,m\} (range)
sed 's/[0-9]\{3,4\}/XXX/g' file.txt  # Match 3-4 digits
```

**Anchors:**
```bash
# ^ (start of line)
sed 's/^/PREFIX: /' file.txt

# $ (end of line)
sed 's/$/ SUFFIX/' file.txt

# \< and \> (word boundaries)
sed 's/\<cat\>/dog/g' file.txt  # Replace whole word "cat"

# Example: Add line numbers
sed = file.txt | sed 'N; s/\n/: /'
```

**Extended Regex (-E):**
```bash
# With -E, don't need to escape +, ?, (), {}, |
sed -E 's/[0-9]+/NUM/g' file.txt
sed -E 's/(error|warning)/ALERT/gi' file.txt
sed -E 's/([0-9]{3})-([0-9]{4})/\1.\2/' file.txt
```

**Hands-On Practice:**
1. Extract all email addresses from text
2. Validate and fix phone numbers
3. Convert camelCase to snake_case
4. Remove HTML tags from text

### Module 4: Multi-line Operations

**Topics:**
- Next (N) command
- Hold space (h, H, g, G, x)
- Multi-line patterns
- Append and insert

**Next Command (N):**
```bash
# Join lines
sed 'N; s/\n/ /' file.txt     # Join every two lines

# Remove line breaks in paragraphs
sed ':a; N; $!ba; s/\n/ /g' file.txt

# Pattern across lines
sed 'N; s/line1\nline2/REPLACED/' file.txt
```

**Hold Space:**
```bash
# Reverse file (tac alternative)
sed '1!G; h; $!d' file.txt

# Explanation:
# 1!G - Append hold space to pattern space (except first line)
# h   - Copy pattern space to hold space
# $!d - Delete pattern space (except last line)

# Print duplicate lines
sed -n 'G; s/\(.*\)\n\1/\1/; t; h' file.txt

# Swap first and last lines
sed '1h; 1d; $!H; $!d; G' file.txt
```

**Append and Insert:**
```bash
# Append after line (a)
sed '3a\New line after line 3' file.txt

# Insert before line (i)
sed '3i\New line before line 3' file.txt

# Append after pattern
sed '/pattern/a\Appended line' file.txt

# Insert multiple lines
sed '1i\Line 1\nLine 2\nLine 3' file.txt
```

**Change Lines (c):**
```bash
# Replace entire line
sed '/pattern/c\Replacement line' file.txt

# Replace line range
sed '1,5c\All first 5 lines replaced' file.txt
```

**Hands-On Practice:**
1. Combine CSV rows spanning multiple lines
2. Remove duplicate consecutive lines
3. Add headers and footers to files
4. Swap sections of a file

---

## Week 3: Sed Scripting and Real-World Use

### Module 5: Sed Scripts and Automation

**Topics:**
- Sed script files
- Comments and formatting
- Multiple commands
- Conditional execution

**Sed Script File:**
```bash
# script.sed
# Remove comments and empty lines
/^#/d
/^$/d

# Replace patterns
s/old/new/g
s/foo/bar/gi

# Add timestamp
1i\# Generated on $(date)

# Run script
sed -f script.sed input.txt
```

**Complex Script Example:**
```bash
# clean_log.sed - Log file cleaner

# Remove DEBUG lines
/\[DEBUG\]/d

# Convert ERROR to uppercase
s/error/ERROR/gi

# Add severity level
s/ERROR/[CRITICAL] ERROR/
s/WARNING/[MEDIUM] WARNING/
s/INFO/[LOW] INFO/

# Format timestamps
s/\([0-9]\{4\}\)-\([0-9]\{2\}\)-\([0-9]\{2\}\)/\1\/\2\/\3/g

# Remove sensitive data
s/password=[^ ]*/password=REDACTED/gi
s/token=[^ ]*/token=REDACTED/gi
```

**Conditional Execution:**
```bash
# Branch (b) and test (t)
sed '/pattern/b skip; s/old/new/; :skip' file.txt

# Label and goto
sed ':loop; s/aa/a/g; t loop' file.txt  # Remove all double 'a'

# Conditional replace
sed '/start/,/end/s/old/new/g' file.txt  # Replace only in range
```

**Hands-On Practice:**
1. Create a script to clean and format CSV files
2. Build a log sanitizer (remove sensitive data)
3. Write a script to normalize config files
4. Create a text formatter for documentation

### Module 6: In-Place Editing and Batch Processing

**Topics:**
- In-place editing (-i)
- Backup files
- Processing multiple files
- Combining with other tools

**In-Place Editing:**
```bash
# Edit file in place (GNU sed)
sed -i 's/old/new/g' file.txt

# Create backup (adds .bak extension)
sed -i.bak 's/old/new/g' file.txt

# Custom backup extension
sed -i.backup 's/old/new/g' file.txt

# BSD/macOS sed (requires argument to -i)
sed -i '' 's/old/new/g' file.txt
sed -i '.bak' 's/old/new/g' file.txt
```

**Batch Processing:**
```bash
# Multiple files
sed -i 's/old/new/g' *.txt

# With find
find . -name "*.conf" -exec sed -i 's/old/new/g' {} \;

# Loop with backup
for file in *.txt; do
    sed -i.bak 's/old/new/g' "$file"
done

# Process and rename
for file in *.txt; do
    sed 's/old/new/g' "$file" > "processed_$file"
done
```

**Combining with Other Tools:**
```bash
# Sed with grep
grep "error" app.log | sed 's/.*ERROR: \(.*\)/\1/'

# Sed with awk
cat data.txt | sed 's/,/ /g' | awk '{print $1, $3}'

# Sed with cut
sed 's/:.*//' /etc/passwd | cut -d: -f1

# Pipeline example
cat access.log | \
    sed -n '/404/p' | \
    sed 's/.*"\(.*\)".*/\1/' | \
    sort | uniq -c | sort -rn
```

**Hands-On Practice:**
1. Batch rename files using sed
2. Update version numbers in multiple config files
3. Convert log format across 100+ files
4. Create a release preparation script

---

## Real-World Use Cases

### Use Case 1: Log Processing

**Extract error counts by hour:**
```bash
cat app.log | \
    sed -n '/ERROR/p' | \
    sed 's/\([0-9]\{4\}-[0-9]\{2\}-[0-9]\{2\} [0-9]\{2\}\):.*/\1/' | \
    sort | uniq -c
```

**Sanitize logs:**
```bash
sed -e 's/password=[^ ]*/password=****/g' \
    -e 's/api_key=[^ ]*/api_key=****/g' \
    -e 's/[0-9]\{3\}-[0-9]\{2\}-[0-9]\{4\}/XXX-XX-XXXX/g' \
    sensitive.log > clean.log
```

### Use Case 2: Configuration Management

**Update database connection strings:**
```bash
sed -i 's/host=localhost/host=db.example.com/g' config/*.ini
sed -i 's/port=3306/port=5432/g' config/*.ini
```

**Environment-specific configs:**
```bash
# dev.sed
s/@ENVIRONMENT@/development/g
s/@DB_HOST@/localhost/g
s/@API_URL@/http:\/\/localhost:8000/g

# prod.sed
s/@ENVIRONMENT@/production/g
s/@DB_HOST@/db.prod.example.com/g
s/@API_URL@/https:\/\/api.example.com/g

sed -f prod.sed config.template > config.prod
```

### Use Case 3: Code Refactoring

**Rename function across codebase:**
```bash
find . -name "*.py" -exec sed -i 's/oldFunction/newFunction/g' {} \;
```

**Update import statements:**
```bash
sed -i 's/from old_module import/from new_module import/g' **/*.py
```

**Add license headers:**
```bash
# license.txt contains header
for file in src/**/*.java; do
    sed -i '1s/^//' "$file"  # Ensure blank first line
    sed -i '1r license.txt' "$file"
done
```

### Use Case 4: Data Transformation

**CSV to SQL:**
```bash
# Input: John,Doe,30
# Output: INSERT INTO users VALUES ('John', 'Doe', 30);

sed "s/\(.*\),\(.*\),\(.*\)/INSERT INTO users VALUES ('\1', '\2', \3);/" data.csv
```

**Format JSON (simple):**
```bash
echo '{"name":"John","age":30}' | \
    sed 's/,/,\n  /g' | \
    sed 's/{/{\n  /' | \
    sed 's/}/\n}/'
```

---

## Common Patterns and Recipes

**Remove trailing whitespace:**
```bash
sed 's/[[:space:]]*$//' file.txt
```

**Remove leading whitespace:**
```bash
sed 's/^[[:space:]]*//' file.txt
```

**Remove both:**
```bash
sed 's/^[[:space:]]*//; s/[[:space:]]*$//' file.txt
```

**Double space file:**
```bash
sed G file.txt
```

**Number lines:**
```bash
sed = file.txt | sed 'N; s/\n/\t/'
```

**Extract IP addresses:**
```bash
sed -n 's/.*\([0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\).*/\1/p' file.txt
```

**Remove HTML tags:**
```bash
sed 's/<[^>]*>//g' file.html
```

**Convert Windows line endings:**
```bash
sed 's/\r$//' file.txt
```

**Add commas to numbers:**
```bash
echo "1234567" | sed ':a;s/\B[0-9]\{3\}\>/,&/;ta'
# Output: 1,234,567
```

---

## Sed Command Reference

| Command | Description | Example |
|---------|-------------|---------|
| `p` | Print | `sed -n '/pattern/p'` |
| `d` | Delete | `sed '/pattern/d'` |
| `s` | Substitute | `sed 's/old/new/g'` |
| `a` | Append | `sed '3a\text'` |
| `i` | Insert | `sed '3i\text'` |
| `c` | Change | `sed '/pat/c\text'` |
| `N` | Next line | `sed 'N; s/\n/ /'` |
| `h` | Hold | `sed 'h'` |
| `g` | Get | `sed 'g'` |
| `x` | Exchange | `sed 'x'` |
| `=` | Line number | `sed ='` |

---

## Flags and Options

| Flag | Description | Example |
|------|-------------|---------|
| `-n` | Suppress output | `sed -n 'p'` |
| `-e` | Multiple commands | `sed -e 's/a/A/' -e 's/b/B/'` |
| `-f` | Script file | `sed -f script.sed` |
| `-i` | In-place edit | `sed -i 's/old/new/'` |
| `-E` | Extended regex | `sed -E 's/[0-9]+/X/'` |
| `g` | Global | `s/old/new/g` |
| `I` | Case insensitive | `s/old/new/I` |
| `p` | Print | `s/old/new/p` |

---

## Debugging Sed

**Print pattern space:**
```bash
sed -n 'l' file.txt  # List (show special characters)
```

**Step-by-step execution:**
```bash
# Add -n and explicit p to see what matches
sed -n 's/pattern/replacement/p' file.txt
```

**Test regex separately:**
```bash
# Use grep first
grep 'pattern' file.txt
# Then apply sed
sed 's/pattern/replacement/' file.txt
```

---

## Best Practices

1. **Test without -i first:** Always test on stdout before editing in-place
2. **Use -i with backup:** `sed -i.bak` creates safety net
3. **Quote patterns:** Use single quotes to avoid shell expansion
4. **Use explicit flags:** Be clear about g, I, p flags
5. **Comment complex scripts:** Add explanations in .sed files
6. **Choose right tool:** Consider awk, perl for very complex tasks
7. **Escape special chars:** Remember to escape `&`, `\`, `/` in replacements

---

## Common Pitfalls

**Greedy matching:**
```bash
# Problem: Matches too much
echo "foo bar baz" | sed 's/f.*b/X/'
# Output: Xaz

# Solution: Use specific patterns
echo "foo bar baz" | sed 's/foo [^ ]*/X/'
# Output: X baz
```

**In-place editing differences:**
```bash
# GNU sed
sed -i 's/old/new/' file.txt

# BSD/macOS sed (requires argument)
sed -i '' 's/old/new/' file.txt
```

**Escaping issues:**
```bash
# Wrong: Shell interprets $
sed "s/old/$new/" file.txt

# Right: Use single quotes
sed 's/old/new/' file.txt

# When you need variables:
sed "s/old/$new/" file.txt  # OK if intentional
sed 's/old/'"$new"'/' file.txt  # Safer
```

---

## Resources

**Documentation:**
- GNU sed manual: `info sed`
- Man page: `man sed`
- Online: https://www.gnu.org/software/sed/manual/

**Books:**
- "sed & awk" by Dale Dougherty
- "Sed & Awk 101 Hacks"

**Online Tools:**
- Sed tester: https://sed.js.org/
- Regex101: https://regex101.com/

---

## Practice Challenges

1. **Challenge 1:** Convert all dates in a log file from DD/MM/YYYY to ISO format
2. **Challenge 2:** Extract all unique IP addresses from Apache access log
3. **Challenge 3:** Create a CSV to JSON converter using only sed
4. **Challenge 4:** Write a sed script to format C code (indentation)
5. **Challenge 5:** Build a markdown to HTML converter (basic)

---

## Assessment Checklist

By the end of this learning plan, you should be able to:

- [ ] Perform basic search and replace operations
- [ ] Use regular expressions in sed patterns
- [ ] Edit files in-place safely
- [ ] Write multi-command sed scripts
- [ ] Use hold space for complex operations
- [ ] Process multiple files efficiently
- [ ] Debug sed commands and scripts
- [ ] Choose when sed is the right tool
- [ ] Combine sed with other Unix tools
- [ ] Handle edge cases (special characters, encoding)
