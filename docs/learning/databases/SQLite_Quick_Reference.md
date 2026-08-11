# SQLite Quick Reference

**Goal:** Practical SQLite knowledge from basics to advanced usage patterns
**Level:** Beginner → Advanced
**Language:** English

---

**Table of Contents:** Introduction | CLI Essentials | Data Types | SQL Operations | Advanced Queries | Indexes & Performance | JSON Support | Full-Text Search | Python Integration | Backup & Administration | Limitations & When to Use

---

## Introduction

**SQLite** is a self-contained, serverless, zero-configuration, transactional SQL database engine. It is the most widely deployed database in the world — embedded in every phone, browser, and countless applications.

**Key properties:**

- **Serverless** — no separate process, reads/writes directly to a file
- **Zero-config** — no setup, no administration
- **Single file** — entire database is one cross-platform file
- **ACID compliant** — full transaction support even after crashes
- **Small footprint** — library is ~600KB

**Best for:** local/embedded apps, prototyping, testing, data analysis, IoT, mobile, caching, file format replacement

**Not ideal for:** high-concurrency write-heavy workloads, client/server architectures, multi-TB datasets

---

## CLI Essentials

### Installation

SQLite CLI (`sqlite3`) is pre-installed on macOS and most Linux distributions.

```bash
# Linux (Debian/Ubuntu)
sudo apt install sqlite3

# Linux (Fedora)
sudo dnf install sqlite

# macOS (already installed, or update via Homebrew)
brew install sqlite

# Windows (download from https://sqlite.org/download.html)
# Or via scoop/choco:
scoop install sqlite
choco install sqlite
```

### Starting the CLI

```bash
# Open or create a database
sqlite3 mydb.db

# Open in-memory database (temporary)
sqlite3 :memory:

# Open read-only
sqlite3 -readonly mydb.db

# Execute SQL directly from command line
sqlite3 mydb.db "SELECT * FROM users;"

# Execute from file
sqlite3 mydb.db < script.sql
```

### Essential Dot Commands

```sql
-- Help
.help                   -- List all dot commands
.help COMMAND           -- Help for specific command

-- Database info
.databases              -- Show attached databases
.tables                 -- List all tables
.tables %pattern%       -- List tables matching pattern
.schema                 -- Show CREATE statements for all objects
.schema tablename       -- Show CREATE statement for specific table
.indexes                -- List all indexes
.indexes tablename      -- List indexes for specific table

-- Output formatting
.mode column            -- Columnar output (readable)
.mode csv               -- CSV output
.mode json              -- JSON output
.mode markdown          -- Markdown table output
.mode table             -- ASCII table output
.mode tabs              -- Tab-separated output
.mode line              -- One value per line
.headers on             -- Show column headers

-- File I/O
.output file.txt        -- Redirect output to file
.output stdout          -- Reset output to terminal
.read script.sql        -- Execute SQL from file
.dump                   -- Dump entire database as SQL
.dump tablename         -- Dump specific table

-- Import/Export
.import file.csv tablename  -- Import CSV into table
.excel                      -- Open results in spreadsheet app
.once file.txt              -- Send next command output to file

-- Session
.quit                   -- Exit (also .exit or Ctrl+D)
.timer on               -- Show execution time for commands
.changes on             -- Show number of rows changed
```

### Recommended CLI Startup

```bash
sqlite3 -column -header mydb.db
```

Or create `~/.sqliterc` for persistent settings:

```sql
.mode column
.headers on
.timer on
.changes on
.nullvalue [NULL]
```

---

## Data Types & Type Affinity

SQLite uses **dynamic typing** with **type affinity**. Any column can hold any type, but columns have a preferred type.

### Storage Classes

| Storage Class | Description                     | Examples              |
|---------------|----------------------------------|----------------------|
| `NULL`        | Missing or unknown value         | `NULL`               |
| `INTEGER`     | Signed integer (1, 2, 3, 4, 6, or 8 bytes) | `42`, `-7`, `0` |
| `REAL`        | 8-byte IEEE floating point       | `3.14`, `-0.001`     |
| `TEXT`        | UTF-8 or UTF-16 string           | `'hello'`            |
| `BLOB`        | Binary data, stored as-is        | `X'48454C4C4F'`      |

### Type Affinity Rules

SQLite maps declared types to affinities:

| Declared Type Contains | Affinity  | Example Declarations         |
|------------------------|-----------|------------------------------|
| `INT`                  | INTEGER   | `INT`, `INTEGER`, `BIGINT`   |
| `CHAR`, `CLOB`, `TEXT` | TEXT      | `VARCHAR(255)`, `TEXT`       |
| `BLOB` or no type      | BLOB      | `BLOB`, (none)               |
| `REAL`, `FLOA`, `DOUB` | REAL      | `REAL`, `DOUBLE`, `FLOAT`    |
| Otherwise              | NUMERIC   | `DECIMAL(10,2)`, `BOOLEAN`   |

### Boolean & Date Handling

SQLite has no native `BOOLEAN` or `DATE` types — use conventions:

```sql
-- Booleans: store as INTEGER (0 = false, 1 = true)
CREATE TABLE tasks (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    done INTEGER DEFAULT 0 CHECK (done IN (0, 1))
);

-- Dates: store as TEXT (ISO 8601) or INTEGER (Unix timestamp)
CREATE TABLE events (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    created_at TEXT DEFAULT (datetime('now')),
    timestamp_unix INTEGER DEFAULT (unixepoch())
);
```

---

## SQL Operations

### CREATE TABLE

```sql
CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL UNIQUE,
    email TEXT NOT NULL,
    age INTEGER CHECK (age > 0),
    bio TEXT DEFAULT '',
    created_at TEXT DEFAULT (datetime('now'))
);

-- Composite primary key
CREATE TABLE order_items (
    order_id INTEGER NOT NULL,
    product_id INTEGER NOT NULL,
    quantity INTEGER NOT NULL DEFAULT 1,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);

-- Strict mode (SQLite 3.37+) — enforces declared types
CREATE TABLE measurements (
    id INTEGER PRIMARY KEY,
    value REAL NOT NULL,
    label TEXT NOT NULL
) STRICT;
```

### INSERT

```sql
-- Single row
INSERT INTO users (username, email, age) VALUES ('alice', 'alice@example.com', 30);

-- Multiple rows
INSERT INTO users (username, email, age) VALUES
    ('bob', 'bob@example.com', 25),
    ('carol', 'carol@example.com', 28);

-- Insert or ignore on conflict
INSERT OR IGNORE INTO users (username, email, age) VALUES ('alice', 'a@b.com', 31);

-- Upsert (INSERT or UPDATE on conflict)
INSERT INTO users (username, email, age) VALUES ('alice', 'new@email.com', 31)
ON CONFLICT(username) DO UPDATE SET
    email = excluded.email,
    age = excluded.age;

-- Insert from SELECT
INSERT INTO archived_users SELECT * FROM users WHERE age > 60;
```

### SELECT

```sql
-- Basic query
SELECT username, email FROM users WHERE age >= 25 ORDER BY username;

-- LIMIT and OFFSET (pagination)
SELECT * FROM users ORDER BY id LIMIT 10 OFFSET 20;

-- DISTINCT
SELECT DISTINCT age FROM users;

-- Aliases
SELECT username AS name, age AS years FROM users;

-- Aggregate functions
SELECT
    COUNT(*) AS total,
    AVG(age) AS avg_age,
    MIN(age) AS youngest,
    MAX(age) AS oldest,
    GROUP_CONCAT(username, ', ') AS names
FROM users;

-- GROUP BY with HAVING
SELECT age, COUNT(*) AS count
FROM users
GROUP BY age
HAVING count > 1
ORDER BY count DESC;

-- CASE expression
SELECT username,
    CASE
        WHEN age < 25 THEN 'junior'
        WHEN age < 40 THEN 'mid'
        ELSE 'senior'
    END AS category
FROM users;

-- COALESCE (first non-NULL)
SELECT COALESCE(bio, 'No bio provided') AS bio FROM users;

-- LIKE and GLOB
SELECT * FROM users WHERE email LIKE '%@example.com';    -- Case-insensitive
SELECT * FROM users WHERE username GLOB '[a-c]*';         -- Case-sensitive, Unix-style
```

### UPDATE

```sql
UPDATE users SET email = 'updated@example.com' WHERE username = 'alice';

-- Update multiple columns
UPDATE users SET age = age + 1, bio = 'Birthday!' WHERE username = 'bob';

-- Update with subquery
UPDATE orders SET status = 'archived'
WHERE user_id IN (SELECT id FROM users WHERE age > 60);

-- UPDATE with RETURNING (SQLite 3.35+)
UPDATE users SET age = age + 1 WHERE username = 'alice' RETURNING *;
```

### DELETE

```sql
DELETE FROM users WHERE username = 'carol';

-- Delete with RETURNING (SQLite 3.35+)
DELETE FROM users WHERE age > 60 RETURNING id, username;

-- Delete all rows (faster than DELETE FROM without WHERE)
DELETE FROM users;
```

### ALTER TABLE

```sql
-- Add column
ALTER TABLE users ADD COLUMN phone TEXT;

-- Rename column (SQLite 3.25+)
ALTER TABLE users RENAME COLUMN phone TO phone_number;

-- Rename table
ALTER TABLE users RENAME TO app_users;

-- Drop column (SQLite 3.35+)
ALTER TABLE users DROP COLUMN bio;
```

---

## Advanced Queries

### JOINs

```sql
-- INNER JOIN (only matching rows)
SELECT o.id, u.username, o.total
FROM orders o
INNER JOIN users u ON o.user_id = u.id;

-- LEFT JOIN (all left rows, NULLs for no match)
SELECT u.username, COUNT(o.id) AS order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id;

-- CROSS JOIN (cartesian product)
SELECT * FROM colors CROSS JOIN sizes;

-- Self-join
SELECT a.username AS user, b.username AS referrer
FROM users a
INNER JOIN users b ON a.referred_by = b.id;
```

### Subqueries

```sql
-- Scalar subquery
SELECT username, (SELECT COUNT(*) FROM orders WHERE user_id = users.id) AS order_count
FROM users;

-- IN subquery
SELECT * FROM users
WHERE id IN (SELECT DISTINCT user_id FROM orders WHERE total > 100);

-- EXISTS
SELECT * FROM users u
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.user_id = u.id);
```

### Common Table Expressions (CTEs)

```sql
-- Simple CTE
WITH active_users AS (
    SELECT * FROM users WHERE last_login > datetime('now', '-30 days')
)
SELECT username, email FROM active_users ORDER BY username;

-- Multiple CTEs
WITH
    big_orders AS (
        SELECT user_id, SUM(total) AS total_spent
        FROM orders GROUP BY user_id HAVING total_spent > 1000
    ),
    user_info AS (
        SELECT id, username, email FROM users
    )
SELECT u.username, b.total_spent
FROM big_orders b
JOIN user_info u ON b.user_id = u.id;

-- Recursive CTE (hierarchical data)
WITH RECURSIVE category_tree(id, name, parent_id, depth) AS (
    SELECT id, name, parent_id, 0
    FROM categories WHERE parent_id IS NULL
    UNION ALL
    SELECT c.id, c.name, c.parent_id, ct.depth + 1
    FROM categories c
    INNER JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT * FROM category_tree ORDER BY depth, name;
```

### Window Functions (SQLite 3.25+)

```sql
-- ROW_NUMBER, RANK, DENSE_RANK
SELECT username, age,
    ROW_NUMBER() OVER (ORDER BY age DESC) AS row_num,
    RANK() OVER (ORDER BY age DESC) AS rank,
    DENSE_RANK() OVER (ORDER BY age DESC) AS dense_rank
FROM users;

-- Partition
SELECT username, department, salary,
    RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dept_rank,
    SUM(salary) OVER (PARTITION BY department) AS dept_total
FROM employees;

-- LAG / LEAD (access previous/next row)
SELECT date, revenue,
    revenue - LAG(revenue) OVER (ORDER BY date) AS daily_change,
    LEAD(revenue) OVER (ORDER BY date) AS next_day
FROM daily_sales;

-- Running total
SELECT date, amount,
    SUM(amount) OVER (ORDER BY date ROWS UNBOUNDED PRECEDING) AS running_total
FROM transactions;

-- Moving average
SELECT date, value,
    AVG(value) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS moving_avg_7d
FROM metrics;
```

---

## Indexes & Performance

### Creating Indexes

```sql
-- Basic index
CREATE INDEX idx_users_email ON users(email);

-- Unique index
CREATE UNIQUE INDEX idx_users_username ON users(username);

-- Composite index (column order matters — leftmost prefix rule)
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at);

-- Partial index (only index rows matching condition)
CREATE INDEX idx_active_users ON users(email) WHERE active = 1;

-- Expression index
CREATE INDEX idx_users_lower_email ON users(lower(email));

-- Drop index
DROP INDEX IF EXISTS idx_users_email;
```

### EXPLAIN & Query Planning

```sql
-- Show query plan (human-readable)
EXPLAIN QUERY PLAN SELECT * FROM users WHERE email = 'alice@example.com';

-- Full EXPLAIN (VM opcodes — advanced)
EXPLAIN SELECT * FROM users WHERE email = 'alice@example.com';
```

**Read the output:**

| Output             | Meaning                                |
|--------------------|----------------------------------------|
| `SCAN users`       | Full table scan (slow on large tables) |
| `SEARCH users`     | Index-assisted lookup (fast)           |
| `USING INDEX ...`  | Which index is used                    |
| `USING COVERING INDEX` | All data from index, no table lookup |

### Performance Tips

```sql
-- Enable WAL mode (better concurrent read performance)
PRAGMA journal_mode = WAL;

-- Set page size before creating tables (default 4096)
PRAGMA page_size = 8192;

-- Analyze tables for query optimizer
ANALYZE;

-- Check database integrity
PRAGMA integrity_check;

-- Memory-mapped I/O (faster reads, set size in bytes)
PRAGMA mmap_size = 268435456;  -- 256MB

-- Batch inserts in a transaction (massive speedup)
BEGIN;
INSERT INTO logs VALUES (...);
INSERT INTO logs VALUES (...);
-- ... thousands more
COMMIT;

-- Foreign key enforcement (off by default!)
PRAGMA foreign_keys = ON;

-- Show database stats
PRAGMA page_count;
PRAGMA page_size;
-- Total size = page_count * page_size
```

### VACUUM

```sql
-- Rebuild database file (reclaim space, defragment)
VACUUM;

-- Auto-vacuum mode (set before creating tables)
PRAGMA auto_vacuum = INCREMENTAL;  -- or FULL
PRAGMA incremental_vacuum(100);    -- Free up to 100 pages
```

---

## JSON Support (SQLite 3.38+)

SQLite has built-in JSON functions — no extension needed since 3.38.

### JSON Functions

```sql
-- Validate JSON
SELECT json_valid('{"name": "alice"}');  -- 1

-- Extract values
SELECT json_extract('{"user": {"name": "alice", "age": 30}}', '$.user.name');
-- Returns: alice

-- Shorthand -> and ->> operators (3.38+)
SELECT data -> '$.user.name' FROM records;    -- Returns JSON value (quoted string)
SELECT data ->> '$.user.name' FROM records;   -- Returns SQL value (unquoted)

-- Modify JSON
SELECT json_set('{"a": 1}', '$.b', 2);           -- {"a":1,"b":2}
SELECT json_insert('{"a": 1}', '$.b', 2);        -- {"a":1,"b":2} (won't overwrite)
SELECT json_replace('{"a": 1}', '$.a', 99);      -- {"a":99}
SELECT json_remove('{"a": 1, "b": 2}', '$.a');   -- {"b":2}

-- Build JSON
SELECT json_object('name', username, 'age', age) FROM users;
SELECT json_array(1, 2, 'three');                 -- [1,2,"three"]
SELECT json_group_array(username) FROM users;     -- ["alice","bob","carol"]
SELECT json_group_object(username, age) FROM users;

-- Iterate over JSON arrays
SELECT value FROM json_each('[10, 20, 30]');
-- Returns rows: 10, 20, 30

-- Query JSON arrays in a column
SELECT u.username, j.value AS tag
FROM users u, json_each(u.tags) j
WHERE j.value = 'admin';
```

### Indexing JSON

```sql
-- Index a JSON field for fast lookups
CREATE INDEX idx_data_name ON records(json_extract(data, '$.name'));

-- Use in queries
SELECT * FROM records WHERE json_extract(data, '$.name') = 'alice';
```

---

## Full-Text Search (FTS5)

FTS5 enables fast text search across large text datasets.

### Setup

```sql
-- Create FTS table
CREATE VIRTUAL TABLE articles_fts USING fts5(
    title,
    body,
    content='articles',       -- External content table
    content_rowid='id'
);

-- Populate from existing table
INSERT INTO articles_fts(rowid, title, body)
SELECT id, title, body FROM articles;

-- Keep in sync with triggers
CREATE TRIGGER articles_ai AFTER INSERT ON articles BEGIN
    INSERT INTO articles_fts(rowid, title, body) VALUES (new.id, new.title, new.body);
END;

CREATE TRIGGER articles_ad AFTER DELETE ON articles BEGIN
    INSERT INTO articles_fts(articles_fts, rowid, title, body) VALUES('delete', old.id, old.title, old.body);
END;

CREATE TRIGGER articles_au AFTER UPDATE ON articles BEGIN
    INSERT INTO articles_fts(articles_fts, rowid, title, body) VALUES('delete', old.id, old.title, old.body);
    INSERT INTO articles_fts(rowid, title, body) VALUES (new.id, new.title, new.body);
END;
```

### Querying

```sql
-- Simple search
SELECT * FROM articles_fts WHERE articles_fts MATCH 'database';

-- Phrase search
SELECT * FROM articles_fts WHERE articles_fts MATCH '"full text search"';

-- Boolean operators
SELECT * FROM articles_fts WHERE articles_fts MATCH 'sqlite AND performance';
SELECT * FROM articles_fts WHERE articles_fts MATCH 'sqlite OR postgres';
SELECT * FROM articles_fts WHERE articles_fts MATCH 'sqlite NOT mysql';

-- Column filter
SELECT * FROM articles_fts WHERE title MATCH 'guide';

-- Prefix search
SELECT * FROM articles_fts WHERE articles_fts MATCH 'data*';

-- Ranked results (BM25)
SELECT *, rank FROM articles_fts WHERE articles_fts MATCH 'database' ORDER BY rank;

-- Highlight matches
SELECT highlight(articles_fts, 0, '<b>', '</b>') AS title,
       snippet(articles_fts, 1, '<b>', '</b>', '...', 32) AS excerpt
FROM articles_fts WHERE articles_fts MATCH 'database';
```

---

## Python Integration

Python includes `sqlite3` in the standard library — no install needed.

### Basic Usage

```python
import sqlite3

# Connect (creates file if not exists)
conn = sqlite3.connect('mydb.db')

# In-memory database
conn = sqlite3.connect(':memory:')

# Enable dict-like row access
conn.row_factory = sqlite3.Row

# Get cursor
cur = conn.cursor()

# Execute SQL
cur.execute('''
    CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        username TEXT NOT NULL UNIQUE,
        email TEXT NOT NULL
    )
''')

# Parameterized queries (NEVER use f-strings or % formatting — SQL injection risk)
cur.execute('INSERT INTO users (username, email) VALUES (?, ?)', ('alice', 'alice@example.com'))

# Named parameters
cur.execute('INSERT INTO users (username, email) VALUES (:name, :email)',
            {'name': 'bob', 'email': 'bob@example.com'})

# Batch insert
users = [('carol', 'carol@example.com'), ('dave', 'dave@example.com')]
cur.executemany('INSERT INTO users (username, email) VALUES (?, ?)', users)

# Commit
conn.commit()

# Query
cur.execute('SELECT * FROM users WHERE username = ?', ('alice',))
row = cur.fetchone()
print(row['username'], row['email'])  # With Row factory

# Fetch all
cur.execute('SELECT * FROM users')
rows = cur.fetchall()
for row in rows:
    print(row['username'])

# Close
conn.close()
```

### Context Manager (Auto-Commit/Rollback)

```python
import sqlite3

with sqlite3.connect('mydb.db') as conn:
    conn.execute('INSERT INTO users (username, email) VALUES (?, ?)',
                 ('eve', 'eve@example.com'))
    # Auto-commits on success, auto-rollback on exception
```

### Practical Patterns

```python
# Export to CSV
import csv

with sqlite3.connect('mydb.db') as conn:
    cur = conn.execute('SELECT * FROM users')
    with open('users.csv', 'w', newline='') as f:
        writer = csv.writer(f)
        writer.writerow([desc[0] for desc in cur.description])
        writer.writerows(cur.fetchall())

# Import CSV
import csv

with sqlite3.connect('mydb.db') as conn:
    with open('data.csv', 'r') as f:
        reader = csv.DictReader(f)
        for row in reader:
            conn.execute('INSERT INTO data (col1, col2) VALUES (?, ?)',
                         (row['col1'], row['col2']))

# Pandas integration
import pandas as pd

conn = sqlite3.connect('mydb.db')
df = pd.read_sql_query('SELECT * FROM users', conn)
df.to_sql('users_backup', conn, if_exists='replace', index=False)
conn.close()
```

---

## Backup & Administration

### Backup Methods

```bash
# Method 1: SQL dump (portable, human-readable)
sqlite3 mydb.db .dump > backup.sql

# Restore from dump
sqlite3 newdb.db < backup.sql

# Method 2: File copy (only when no writes in progress)
# Ensure WAL mode checkpoint first:
sqlite3 mydb.db "PRAGMA wal_checkpoint(TRUNCATE);"
cp mydb.db mydb_backup.db

# Method 3: Online backup API (safe while database is in use)
sqlite3 mydb.db ".backup backup.db"
```

### Python Backup

```python
import sqlite3

source = sqlite3.connect('mydb.db')
backup = sqlite3.connect('backup.db')
source.backup(backup)
backup.close()
source.close()
```

### Import/Export CSV

```bash
# Import CSV
sqlite3 mydb.db <<'EOF'
.mode csv
.import data.csv tablename
EOF

# Export to CSV
sqlite3 -header -csv mydb.db "SELECT * FROM users;" > users.csv

# Export to JSON
sqlite3 -json mydb.db "SELECT * FROM users;" > users.json
```

### Attach Multiple Databases

```sql
ATTACH DATABASE 'other.db' AS other;

SELECT * FROM other.users;

-- Copy table between databases
INSERT INTO main.users SELECT * FROM other.users;

DETACH DATABASE other;
```

### Database Info Queries

```sql
-- List all tables with row counts
SELECT name, (SELECT COUNT(*) FROM pragma_table_info(name)) AS columns
FROM sqlite_master WHERE type = 'table' ORDER BY name;

-- Table details
PRAGMA table_info(users);
PRAGMA table_xinfo(users);      -- Includes hidden columns

-- Database size
SELECT page_count * page_size AS size_bytes FROM pragma_page_count(), pragma_page_size();

-- Foreign key check
PRAGMA foreign_key_check;

-- Compile options (what features are available)
PRAGMA compile_options;

-- SQLite version
SELECT sqlite_version();
```

---

## Date & Time Functions

```sql
-- Current date/time
SELECT date('now');                          -- 2026-08-12
SELECT time('now');                          -- 14:30:00
SELECT datetime('now');                      -- 2026-08-12 14:30:00
SELECT unixepoch();                          -- 1723470600

-- Modifiers
SELECT date('now', '+7 days');               -- 7 days from now
SELECT date('now', '-1 month');              -- 1 month ago
SELECT datetime('now', '+2 hours', '+30 minutes');
SELECT date('now', 'start of month');        -- First day of current month
SELECT date('now', 'start of year', '+3 months', '-1 day');  -- End of Q1

-- Formatting
SELECT strftime('%Y-%m-%d %H:%M', 'now');
SELECT strftime('%W', 'now');                -- Week number

-- Parse Unix timestamp
SELECT datetime(1723470600, 'unixepoch');
SELECT datetime(1723470600, 'unixepoch', 'localtime');

-- Date difference (days between)
SELECT julianday('2026-12-31') - julianday('2026-01-01') AS days_in_year;
```

---

## Useful PRAGMAs Reference

| PRAGMA                        | Purpose                                | Recommended Value      |
|-------------------------------|----------------------------------------|------------------------|
| `journal_mode = WAL`          | Write-Ahead Logging (better concurrency) | `WAL` for most apps  |
| `foreign_keys = ON`           | Enforce FK constraints (off by default!) | `ON` always          |
| `busy_timeout = 5000`         | Wait N ms instead of failing on lock   | `5000`                 |
| `synchronous = NORMAL`        | Durability vs speed tradeoff           | `NORMAL` with WAL      |
| `cache_size = -20000`         | Page cache in KB (negative = KB)       | `-20000` (20MB)        |
| `temp_store = MEMORY`         | Temp tables in memory                  | `MEMORY`               |
| `mmap_size = 268435456`       | Memory-mapped I/O (bytes)              | `268435456` (256MB)    |
| `optimize`                    | Run after schema/data changes          | Run periodically       |

### Production-Ready Connection Setup

```sql
PRAGMA journal_mode = WAL;
PRAGMA foreign_keys = ON;
PRAGMA busy_timeout = 5000;
PRAGMA synchronous = NORMAL;
PRAGMA cache_size = -20000;
PRAGMA temp_store = MEMORY;
PRAGMA mmap_size = 268435456;
```

---

## Limitations & Comparison

### What SQLite Cannot Do

| Feature                  | SQLite                    | PostgreSQL / MySQL         |
|--------------------------|---------------------------|----------------------------|
| Concurrent writes        | Single writer at a time   | Multiple concurrent writers|
| `RIGHT JOIN` / `FULL OUTER JOIN` | `FULL OUTER` since 3.39 | Full support        |
| `ALTER TABLE`            | Limited (no modify column type) | Full support          |
| User management          | None (file permissions)   | Roles, grants, row-level   |
| Network access           | Local file only           | Client/server TCP          |
| Stored procedures        | None                      | Full support               |
| Data size                | Practical limit ~1TB      | Multi-TB                   |

### Decision Guide

**Choose SQLite when:**

- Single application accessing the data
- Dataset under 100GB
- Embedded / mobile / desktop / CLI application
- Development and testing
- Data analysis and prototyping
- File format replacement (config, logs, cache)
- Read-heavy workload

**Choose PostgreSQL/MySQL when:**

- Multiple applications or users need concurrent write access
- Client/server architecture required
- Complex access control needed
- Write-heavy with high concurrency
- Dataset approaching or exceeding 1TB
