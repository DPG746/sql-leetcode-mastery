# 01: GROUP BY & HAVING - Core Concepts

## What is GROUP BY?

**GROUP BY** groups rows with the same values in specified columns into summary rows.

```sql
SELECT dept_id, COUNT(*) as emp_count
FROM employees
GROUP BY dept_id;
```

This groups employees by department and counts how many in each.

---

## Aggregate Functions

Used with GROUP BY:

- **COUNT()** - Number of rows
- **SUM()** - Total of numeric column
- **AVG()** - Average of numeric column
- **MIN()** - Minimum value
- **MAX()** - Maximum value

```sql
SELECT 
    dept_id,
    COUNT(*) as total_employees,
    AVG(salary) as avg_salary,
    MIN(salary) as lowest_salary,
    MAX(salary) as highest_salary,
    SUM(salary) as total_salary
FROM employees
GROUP BY dept_id;
```

---

## COUNT() vs COUNT(column)

```sql
-- COUNT(*) - Counts all rows including NULLs
SELECT COUNT(*) FROM employees;  -- 100

-- COUNT(column) - Counts non-NULL values
SELECT COUNT(manager_id) FROM employees;  -- 95 (5 NULLs skipped)
```

---

## GROUP BY with Multiple Columns

```sql
SELECT dept_id, job_title, COUNT(*) as count
FROM employees
GROUP BY dept_id, job_title;
```

Groups by department first, then by job title within each department.

---

## HAVING Clause

Filters groups (not individual rows like WHERE).

```sql
SELECT dept_id, COUNT(*) as emp_count
FROM employees
GROUP BY dept_id
HAVING COUNT(*) > 5;  -- Only departments with more than 5 employees
```

### WHERE vs HAVING

```sql
-- WHERE: Filter before grouping
SELECT dept_id, COUNT(*) as emp_count
FROM employees
WHERE salary > 50000  -- Filter individuals first
GROUP BY dept_id;

-- HAVING: Filter after grouping
SELECT dept_id, COUNT(*) as emp_count
FROM employees
GROUP BY dept_id
HAVING COUNT(*) > 10;  -- Filter groups based on group size
```

---

## Common Patterns

### Count distinct values
```sql
SELECT dept_id, COUNT(DISTINCT job_title) as job_count
FROM employees
GROUP BY dept_id;
```

### Conditional counting
```sql
SELECT dept_id,
    COUNT(*) as total,
    COUNT(CASE WHEN salary > 80000 THEN 1 END) as high_paid
FROM employees
GROUP BY dept_id;
```

### Group and filter
```sql
SELECT dept_id, AVG(salary) as avg_sal
FROM employees
GROUP BY dept_id
HAVING AVG(salary) > 75000;
```

---

## ORDER BY with GROUP BY

```sql
SELECT dept_id, COUNT(*) as emp_count
FROM employees
GROUP BY dept_id
ORDER BY emp_count DESC;  -- Highest count first
```

---

## Common Mistakes

### ❌ Mistake 1: SELECT column not in GROUP BY (without aggregate)
```sql
SELECT emp_name, dept_id, COUNT(*) FROM employees GROUP BY dept_id;
-- ERROR: emp_name must be aggregated or in GROUP BY
```

### ✅ Fix:
```sql
SELECT COUNT(*), dept_id FROM employees GROUP BY dept_id;
```

### ❌ Mistake 2: Using WHERE instead of HAVING
```sql
SELECT dept_id, COUNT(*) FROM employees
GROUP BY dept_id
WHERE COUNT(*) > 5;  -- ERROR: Can't use aggregate in WHERE
```

### ✅ Fix:
```sql
SELECT dept_id, COUNT(*) FROM employees
GROUP BY dept_id
HAVING COUNT(*) > 5;
```

### ❌ Mistake 3: Filtering after grouping in WHERE
```sql
SELECT dept_id, AVG(salary) FROM employees
WHERE salary > 50000  -- Filters before grouping
GROUP BY dept_id;
```

### ✅ If you want to filter groups:
```sql
SELECT dept_id, AVG(salary) FROM employees
GROUP BY dept_id
HAVING AVG(salary) > 50000;
```

---

## Execution Order

```
1. FROM - Load table
2. WHERE - Filter rows
3. GROUP BY - Group rows
4. HAVING - Filter groups
5. SELECT - Choose columns
6. ORDER BY - Sort results
```

---

**Next:** Try practice problems in `03-practice-problems.md`!
