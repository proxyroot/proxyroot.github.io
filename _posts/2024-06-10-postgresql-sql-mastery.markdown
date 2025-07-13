---
title: "PostgreSQL SQL Mastery Guide"
date: 2024-06-10
categories: [sql, postgresql, database]
summary: "A practical, beginner-to-advanced guide to mastering SQL in PostgreSQL. Covers essential queries, advanced features, performance tips, and real-world examples with annotated code blocks. Perfect for developers and data analysts looking to level up their SQL skills."
---

This guide provides a hands-on walkthrough of PostgreSQL SQL, from basic queries to advanced optimization and real-world scenarios. Each section includes annotated code and practical tips to help you write efficient, effective SQL for any project.

# 🐘 PostgreSQL SQL Mastery Guide

---

## 1. 🧱 Basics

### SELECT, FROM, WHERE, ORDER BY, LIMIT

Used to retrieve data.

```sql
SELECT name, salary FROM employees
WHERE department = 'Engineering'
ORDER BY salary DESC
LIMIT 5;
```

### Filtering with AND, OR, IN, BETWEEN, LIKE

```sql
SELECT * FROM employees
WHERE salary BETWEEN 60000 AND 90000
AND department IN ('HR', 'Engineering')
AND name LIKE 'A%';
```

### Aggregations: COUNT, SUM, AVG, MIN, MAX

```sql
SELECT department, COUNT(*) AS total, AVG(salary) AS avg_salary
FROM employees
GROUP BY department;
```

### GROUP BY and HAVING

```sql
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 70000;
```

---

## 2. 🔄 Intermediate

### JOIN types: INNER, LEFT, RIGHT, FULL

```sql
SELECT e.name, d.name AS department
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id;
```

### Subqueries (SELECT, FROM, WHERE)

```sql
SELECT name
FROM employees
WHERE salary > (
  SELECT AVG(salary) FROM employees
);
```

### CASE WHEN logic

```sql
SELECT name, salary,
  CASE
    WHEN salary > 90000 THEN 'High'
    WHEN salary > 60000 THEN 'Medium'
    ELSE 'Low'
  END AS salary_band
FROM employees;
```

### DISTINCT, UNION, EXCEPT, INTERSECT

```sql
SELECT DISTINCT department FROM employees;

SELECT name FROM engineers
UNION
SELECT name FROM hr;
```

### Window Functions: ROW_NUMBER, RANK, OVER

```sql
SELECT name, salary,
  ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rank
FROM employees;
```

---

## 3. 🧠 Advanced

### CTEs (WITH)

```sql
WITH avg_salary AS (
  SELECT department_id, AVG(salary) AS avg
  FROM employees
  GROUP BY department_id
)
SELECT e.name, e.salary
FROM employees e
JOIN avg_salary a ON e.department_id = a.department_id
WHERE e.salary > a.avg;
```

### Recursive Queries

```sql
WITH RECURSIVE nums(n) AS (
  SELECT 1
  UNION
  SELECT n + 1 FROM nums WHERE n < 5
)
SELECT * FROM nums;
```

### JSON and JSONB

```sql
SELECT data->>'name' AS name
FROM users
WHERE data->>'active' = 'true';
```

### Indexes and Performance

```sql
CREATE INDEX idx_salary ON employees(salary);

EXPLAIN ANALYZE
SELECT * FROM employees WHERE salary > 70000;
```

### Functions in PLpgSQL

```sql
CREATE FUNCTION high_earners(threshold INT)
RETURNS TABLE(name TEXT, salary INT) AS $$
BEGIN
  RETURN QUERY
  SELECT name, salary FROM employees WHERE salary > threshold;
END;
$$ LANGUAGE plpgsql;
```

### Transactions and Locking

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

---

## 4. 🛠 Real-World Practice

### Sample Schema: Payroll

```sql
-- employees table
-- departments table
-- payroll_entries table
```

### Build Query from Requirement

**Q:** Show department-wise top earner(s) and average salary

```sql
WITH stats AS (
  SELECT department_id, MAX(salary) AS max_salary, AVG(salary) AS avg_salary
  FROM employees GROUP BY department_id
),
top_earners AS (
  SELECT e.name, e.salary, d.name AS department
  FROM employees e
  JOIN stats s ON e.department_id = s.department_id AND e.salary = s.max_salary
  JOIN departments d ON d.id = e.department_id
)
SELECT * FROM top_earners;
```

### Debug with EXPLAIN

```sql
EXPLAIN ANALYZE
SELECT * FROM employees WHERE department_id = 2 AND salary > 80000;
```

---

## 💡 Tip
Practice using PostgreSQL on a local setup or tools like [pgAdmin](https://www.pgadmin.org/) or [db-fiddle](https://www.db-fiddle.com/).

---

---

## 🚀 Indexes and Query Optimization

### 🔹 What is an Index?

An **index** is a data structure that improves the speed of data retrieval at the cost of slower writes and more storage.

Think of it like a book index — it tells you exactly where to find information without scanning the whole book.

---

### 📘 Types of Indexes in PostgreSQL

- **B-Tree (default)**: Great for equality and range queries.
- **Hash**: Good for equality only (rarely used).
- **GIN**: Useful for full-text search and JSONB.
- **BRIN**: Efficient for very large tables with naturally ordered data.
- **Composite Index**: Index on multiple columns.

---

### ⚙️ Creating Indexes

```sql
-- Index on one column
CREATE INDEX idx_salary ON employees(salary);

-- Index on multiple columns
CREATE INDEX idx_dept_salary ON employees(department_id, salary);

-- JSONB index
CREATE INDEX idx_json_name ON users USING GIN ((data->>'name'));
```

---

### 🧪 Using EXPLAIN to Analyze Queries

```sql
EXPLAIN ANALYZE
SELECT * FROM employees WHERE salary > 70000;
```

- Look for `Seq Scan` → can be replaced with `Index Scan` if an index exists.
- Check actual timing and row estimates.

---

### 📈 Real-World Use Case: Optimizing a Report Query

#### Scenario:

> Show the top 3 highest-paid employees per department (out of 10 million rows).

---

### 🛠 Step-by-Step Solution

```sql
-- Index for faster filtering and ordering
CREATE INDEX idx_dept_salary ON employees(department_id, salary DESC);
```

```sql
-- Optimized Query
SELECT *
FROM (
  SELECT name, department_id, salary,
         ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rank
  FROM employees
) ranked
WHERE rank <= 3;
```

- Uses **window function** to rank salaries per department
- Index helps sort within partitions
- Avoids full table sort

---

### ⚡ Tips to Speed Up Queries

- Use `EXPLAIN ANALYZE` regularly
- Use **indexes** wisely on columns used in `WHERE`, `JOIN`, `ORDER BY`
- Avoid `SELECT *` in production queries
- Avoid unnecessary `DISTINCT` and nested subqueries
- Batch inserts and updates
- Normalize and denormalize when needed
- Materialize expensive CTEs (PostgreSQL 12+): `MATERIALIZED`

---

### 🧠 Summary

Indexes can dramatically reduce query time — especially on large datasets. But use them carefully:
- Too many indexes = slow writes
- Use compound indexes when queries filter on multiple columns
- Always validate with `EXPLAIN ANALYZE`

--- 