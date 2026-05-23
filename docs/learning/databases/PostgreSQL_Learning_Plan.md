# PostgreSQL Quick Reference

**Goal:** Comprehensive PostgreSQL database management knowledge from basics to advanced
**Level:** Beginner → Advanced  
**Language:** English 

---

 **Table of Contents:** Introduction | Prerequisites | Learning Path | 8 Modules | Projects | Tools | Resources | Assessment

---

## Introduction

**PostgreSQL** (aka "Postgres") is an open-source object-relational database management system (ORDBMS) emphasizing standards compliance and extensibility.

 **Advantages:** Open source, ACID compliant, rich features (JSON, GIS, FTS), extensible, enterprise-ready
 **Use Cases:** Web apps, data warehousing, geospatial, finance, IoT, enterprise

---

## Modules Overview

### Module 1: Basics & Installation (Week 1)
- Installation (Ubuntu/macOS/Docker)
- Core concepts & architecture
- psql command-line tool
- First connection & setup

### Module 2: SQL Fundamentals (Weeks 2-3)
- Data types (numeric, text, date, JSON, arrays)
- CRUD operations
- WHERE conditions & operators
- JOINs (INNER, LEFT, RIGHT, FULL, CROSS)

### Module 3: Database Design (Week 4)
- Normalization (1NF, 2NF, 3NF)
- Constraints (NOT NULL, UNIQUE, CHECK, FOREIGN KEY)
- Schema design patterns

### Module 4: Advanced SQL (Weeks 5-6)
- Aggregation & GROUP BY
- Subqueries
- Window functions (ROW_NUMBER, RANK, LAG, LEAD)
- CTEs (Common Table Expressions)
- Recursive queries

### Module 5: Indexes & Performance (Week 7)
- Index types (B-tree, Hash, GiST, GIN)
- EXPLAIN ANALYZE
- Query optimization
- VACUUM & ANALYZE

### Module 6: Advanced Features (Week 8)
- JSON/JSONB operations
- Array types
- Views & Materialized Views
- Stored procedures & functions
- Triggers

### Module 7: Transactions (Week 9)
- ACID properties
- Isolation levels
- Locks & deadlocks
- Concurrency control

### Module 8: Administration (Week 10)
- User & role management
- Backup/restore (pg_dump, pg_restore)
- Monitoring & maintenance
- Security best practices

---

## Quick Start Examples

### Installation (Docker)

```bash
docker run -d \
  --name postgres-dev \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_USER=dev \
  -e POSTGRES_DB=testdb \
  -p 5432:5432 \
  postgres:16-alpine
```

### Basic CRUD

```sql
-- Create table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert
INSERT INTO users (username, email)
VALUES ('jdoe', 'john@example.com')
RETURNING *;

-- Select
SELECT * FROM users WHERE created_at > NOW() - INTERVAL '7 days';

-- Update
UPDATE users SET email = 'new@example.com' WHERE id = 1;

-- Delete
DELETE FROM users WHERE id = 1;
```

### Advanced Query Example

```sql
-- Window function + CTE
WITH monthly_sales AS (
    SELECT
        DATE_TRUNC('month', order_date) as month,
        customer_id,
        SUM(total) as monthly_total
    FROM orders
    GROUP BY 1, 2
)
SELECT
    month,
    customer_id,
    monthly_total,
    RANK() OVER (PARTITION BY month ORDER BY monthly_total DESC) as customer_rank
FROM monthly_sales;
```

---

## Practical Projects

### Project 1: Blog Platform (Easy)
- Database schema (users, posts, comments, tags)
- Full-text search
- Related posts
- User statistics

### Project 2: E-commerce Backend (Medium)
- Complex schema (products, orders, inventory, reviews)
- Transactional operations
- Inventory management
- Sales reports
- Performance optimization

### Project 3: Analytics Dashboard (Advanced)
- Time-series data
- Materialized views
- Window functions
- Real-time queries
- Partitioning

---

## Essential Tools

| Tool | Platform | Cost | Description |
|------|----------|------|-------------|
| **pgAdmin** | Cross-platform | Free | Official admin tool |
| **DBeaver** | Cross-platform | Free | Universal DB client |
| **DataGrip** | Cross-platform | Paid | JetBrains IDE |
| **pgcli** | CLI | Free | Enhanced psql |

---

## Resources

### Official Documentation
- **PostgreSQL Docs:** https://www.postgresql.org/docs/
- **PostgreSQL Tutorial:** https://www.postgresqltutorial.com

### Online Courses
- Udemy: "The Complete SQL Bootcamp"
- Coursera: "Databases and SQL for Data Science"
- DataCamp: "Introduction to SQL"

### Books
- "PostgreSQL: Up and Running" (O'Reilly)
- "The Art of PostgreSQL" (Dimitri Fontaine)
- "Mastering PostgreSQL" (Hans-Jürgen Schönig)

### Community
- PostgreSQL Mailing Lists
- Stack Overflow: `[postgresql]` tag
- Reddit: r/PostgreSQL
- Discord: PostgreSQL Community

---

## Learning Milestones

### Weeks 1-2: Basics
- [ ] PostgreSQL installed and configured
- [ ] psql proficiency
- [ ] Basic CRUD operations
- [ ] Simple SELECT queries

### Weeks 3-4: Intermediate
- [ ] JOINs mastery
- [ ] Aggregation functions
- [ ] Subqueries
- [ ] Views

### Weeks 5-6: Advanced
- [ ] Window functions
- [ ] CTEs & recursive queries
- [ ] Indexes & optimization
- [ ] JSON handling

### Weeks 7-8: Expert
- [ ] Stored procedures & triggers
- [ ] Transaction management
- [ ] Backup/restore
- [ ] Production-ready project

---

## Performance Tips

```sql
-- Bad: Function in WHERE (no index use)
SELECT * FROM orders WHERE EXTRACT(YEAR FROM order_date) = 2024;

-- Good: Sargable query (index-friendly)
SELECT * FROM orders
WHERE order_date >= '2024-01-01' AND order_date < '2025-01-01';

-- Create appropriate index
CREATE INDEX idx_orders_date ON orders(order_date);
```

---

## Backup Example

```bash
#!/bin/bash
# Automated backup script

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backup/postgres"
DB_NAME="production"

pg_dump -U postgres -d "$DB_NAME" -F c -f "$BACKUP_DIR/${DB_NAME}_${DATE}.dump"

# Keep 7 days
find "$BACKUP_DIR" -name "*.dump" -mtime +7 -delete
```

---

## Summary

**Learning Path:**
```
Installation → SQL Basics → Design → Advanced SQL → Performance → Admin
```

**Key Skills:**
-  SQL query writing
-  Database schema design
-  Performance optimization
-  Backup & maintenance
-  Security best practices

**Next Steps:**
1. Build practical projects
2. Manage production environments
3. Explore specialized topics (PostGIS, replication)
4. Consider PostgreSQL certification

---

**Last Updated:** 2026-03-18
**Version:** 1.0
**License:** CC BY 4.0
**Author:** Claude (Anthropic)

**Happy Learning! **
