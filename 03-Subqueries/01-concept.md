# 01: Subqueries - Core Concepts

## What is a Subquery?

A **subquery** (or inner query) is a query nested inside another query.

```sql
-- Main query
SELECT * FROM employees
WHERE dept_id IN (
    -- Subquery
    SELECT dept_id FROM departments WHERE budget > 100000
);
```

---

## Types of Subqueries

### 1. Scalar Subquery (returns one value)

```sql
SELECT emp_name, salary,
    (SELECT AVG(salary) FROM employees) as avg_salary
FROM employees;
```

Returns one value used in SELECT, WHERE, etc.

---

### 2. Row Subquery (returns one row with multiple columns)

```sql
SELECT * FROM employees
WHERE (dept_id, salary) = (
    SELECT dept_id, salary FROM employees WHERE emp_id = 5
);
```

---

### 3. Table Subquery (returns multiple rows/columns)

```sql
SELECT * FROM employees
WHERE dept_id IN (
    SELECT dept_id FROM departments WHERE budget > 100000
);
```

---

## Subquery Locations

### In SELECT Clause
```sql
SELECT emp_name, salary,
    (SELECT MAX(salary) FROM employees) as max_sal
FROM employees;
```

### In WHERE Clause
```sql
SELECT * FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

### In FROM Clause
```sql
SELECT * FROM (
    SELECT emp_id, salary FROM employees WHERE salary > 50000
) AS high_earners;
```

### In HAVING Clause
```sql
SELECT dept_id, COUNT(*) as emp_count
FROM employees
GROUP BY dept_id
HAVING COUNT(*) > (SELECT AVG(count) FROM ...)
```

---

## Correlated vs Non-Correlated

### Non-Correlated (runs once)
```sql
SELECT * FROM employees e
WHERE salary > (SELECT AVG(salary) FROM employees);
-- Subquery runs once, compares to each row
```

### Correlated (runs for each row)
```sql
SELECT * FROM employees e
WHERE salary > (
    SELECT AVG(salary) FROM employees 
    WHERE dept_id = e.dept_id
);
-- Subquery runs for EACH employee row
```

---

## IN, EXISTS, ANY, ALL

### IN
```sql
SELECT * FROM employees
WHERE dept_id IN (1, 2, 3);

-- With subquery
SELECT * FROM employees
WHERE dept_id IN (SELECT dept_id FROM departments WHERE budget > 50000);
```

### EXISTS
```sql
SELECT * FROM employees e
WHERE EXISTS (
    SELECT 1 FROM orders WHERE emp_id = e.emp_id
);
-- Returns true if subquery returns any rows
```

### ANY
```sql
SELECT * FROM employees
WHERE salary > ANY (SELECT salary FROM employees WHERE dept_id = 2);
-- Salary > minimum of dept 2 salaries
```

### ALL
```sql
SELECT * FROM employees
WHERE salary > ALL (SELECT salary FROM employees WHERE dept_id = 2);
-- Salary > maximum of dept 2 salaries (must be higher than all)
```

---

## Performance Considerations

### ❌ Inefficient
```sql
SELECT emp_name FROM employees
WHERE dept_id IN (SELECT dept_id FROM employees WHERE hire_date > '2020-01-01');
```

### ✅ Better (with JOIN)
```sql
SELECT DISTINCT e.emp_name 
FROM employees e
INNER JOIN employees e2 ON e.dept_id = e2.dept_id
WHERE e2.hire_date > '2020-01-01';
```

**General Rule:** Use JOINs when possible, subqueries when necessary.

---

## Common Patterns

### Find rows with max value per group
```sql
SELECT * FROM employees e
WHERE salary = (
    SELECT MAX(salary) FROM employees 
    WHERE dept_id = e.dept_id
);
```

### Find missing records
```sql
SELECT * FROM customers
WHERE customer_id NOT IN (
    SELECT DISTINCT customer_id FROM orders
);
```

### Compare to aggregate
```sql
SELECT * FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

---

## Common Mistakes

### ❌ Mistake 1: Subquery returns multiple rows for scalar context
```sql
SELECT * FROM employees
WHERE salary = (SELECT salary FROM employees WHERE dept_id = 1);
-- ERROR if dept_id=1 has multiple employees!
```

### ✅ Fix:
```sql
SELECT * FROM employees
WHERE salary IN (SELECT salary FROM employees WHERE dept_id = 1);
```

### ❌ Mistake 2: Forgetting correlation
```sql
SELECT * FROM employees e
WHERE salary > (SELECT AVG(salary) FROM employees);
-- Compares to overall average, not dept average
```

### ✅ Fix:
```sql
SELECT * FROM employees e
WHERE salary > (
    SELECT AVG(salary) FROM employees 
    WHERE dept_id = e.dept_id
);
```

### ❌ Mistake 3: Performance - correlated subquery in loop
```sql
SELECT * FROM orders o
WHERE emp_id IN (
    SELECT emp_id FROM employees 
    WHERE salary > (SELECT AVG(salary) FROM employees WHERE dept_id = e.dept_id)
);
-- Runs subquery for EVERY order
```

### ✅ Fix: Use JOIN
```sql
SELECT o.* FROM orders o
INNER JOIN employees e ON o.emp_id = e.emp_id
INNER JOIN (
    SELECT dept_id, AVG(salary) as avg_sal FROM employees GROUP BY dept_id
) d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_sal;
```

---

## CTEs (Common Table Expressions) as Alternative

```sql
WITH high_earners AS (
    SELECT emp_id, salary FROM employees WHERE salary > 100000
)
SELECT * FROM orders
WHERE emp_id IN (SELECT emp_id FROM high_earners);
```

More readable than nested subqueries!

---

**Next:** Try practice problems in `03-practice-problems.md`!
