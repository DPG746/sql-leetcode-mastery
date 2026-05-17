# 04: CASE WHEN & NULL Handling - Core Concepts

## What is CASE WHEN?

**CASE WHEN** creates conditional logic in queries.

**Two forms:**

### Simple CASE
```sql
SELECT emp_name,
CASE salary
    WHEN 100000 THEN 'Senior'
    WHEN 75000 THEN 'Mid'
    ELSE 'Junior'
END as level
FROM employees;
```

### Searched CASE (more flexible)
```sql
SELECT emp_name,
CASE 
    WHEN salary >= 100000 THEN 'Senior'
    WHEN salary >= 75000 THEN 'Mid'
    ELSE 'Junior'
END as level
FROM employees;
```

**Searched CASE is more commonly used.**

---

## CASE WHEN in Different Clauses

### In SELECT
```sql
SELECT 
    emp_name,
    salary,
    CASE WHEN salary > 80000 THEN 'High' ELSE 'Low' END as salary_level
FROM employees;
```

### In WHERE
```sql
SELECT * FROM employees
WHERE CASE WHEN dept_id IN (1,2) THEN salary ELSE 0 END > 70000;
```

### In ORDER BY
```sql
SELECT * FROM orders
ORDER BY 
    CASE WHEN status = 'urgent' THEN 1 ELSE 2 END,
    order_date;
```

### With Aggregates (Conditional Aggregation)
```sql
SELECT dept_id,
    COUNT(*) as total_emp,
    COUNT(CASE WHEN salary > 60000 THEN 1 END) as high_paid,
    SUM(CASE WHEN salary > 60000 THEN salary ELSE 0 END) as high_paid_total
FROM employees
GROUP BY dept_id;
```

---

## NULL Handling Functions

### ISNULL (SQL Server, MySQL)
```sql
SELECT emp_name, ISNULL(manager_id, 'No Manager') FROM employees;
```

### IFNULL (MySQL)
```sql
SELECT emp_name, IFNULL(manager_id, 0) FROM employees;
```

### COALESCE (Standard SQL - works in all databases)
```sql
SELECT emp_name, COALESCE(manager_id, dept_id, 0) FROM employees;
-- Returns first non-NULL value
```

### IS NULL / IS NOT NULL
```sql
SELECT * FROM employees WHERE manager_id IS NULL;
SELECT * FROM employees WHERE manager_id IS NOT NULL;
```

---

## NULL Comparisons

**Important:** NULL is never equal to anything, including NULL!

```sql
SELECT NULL = NULL;  -- Result: NULL (unknown), not TRUE!

-- This won't work:
SELECT * FROM employees WHERE manager_id = NULL;  -- Wrong!

-- Use IS NULL:
SELECT * FROM employees WHERE manager_id IS NULL;  -- Correct!
```

---

## CASE WHEN for NULL Handling

```sql
SELECT emp_name,
CASE 
    WHEN manager_id IS NULL THEN 'No Manager'
    ELSE 'Has Manager'
END as manager_status
FROM employees;
```

---

## Common Patterns

### Flag columns
```sql
SELECT order_id,
CASE WHEN status = 'completed' THEN 1 ELSE 0 END as is_completed
FROM orders;
```

### Bucket values
```sql
SELECT emp_id,
CASE 
    WHEN salary < 50000 THEN 'Entry'
    WHEN salary < 75000 THEN 'Mid'
    WHEN salary < 100000 THEN 'Senior'
    ELSE 'Executive'
END as salary_band
FROM employees;
```

### Handle business logic
```sql
SELECT order_id,
CASE 
    WHEN days_late > 30 THEN 'Critical'
    WHEN days_late > 7 THEN 'Urgent'
    WHEN days_late > 0 THEN 'Late'
    ELSE 'On Time'
END as status
FROM orders;
```

---

## Common Mistakes

### ❌ Mistake 1: Using = for NULL
```sql
SELECT * FROM employees WHERE phone = NULL;  -- Returns no rows!
```

### ✅ Fix:
```sql
SELECT * FROM employees WHERE phone IS NULL;
```

### ❌ Mistake 2: Comparing NULL values
```sql
WHERE column1 = column2;  -- If either is NULL, result is NULL (unknown)
```

### ✅ Fix:
```sql
WHERE COALESCE(column1, '') = COALESCE(column2, '');
```

### ❌ Mistake 3: CASE without ELSE
```sql
CASE WHEN salary > 100000 THEN 'High'
-- Returns NULL if condition false!
```

### ✅ Fix:
```sql
CASE WHEN salary > 100000 THEN 'High' ELSE 'Low' END
```

---

## NULLIF Function

Returns NULL if values are equal:

```sql
SELECT order_id,
    NULLIF(discount_amount, 0) as discount  -- NULL if 0, else discount amount
FROM orders;
```

---

## Order of Operations with NULL

```sql
-- All these return NULL if column is NULL:
SELECT column1 + 5 FROM table;      -- NULL + 5 = NULL
SELECT column1 || 'text' FROM table; -- NULL || 'text' = NULL
SELECT UPPER(column1) FROM table;    -- UPPER(NULL) = NULL
```

**Use COALESCE to handle:**
```sql
SELECT COALESCE(column1, 0) + 5;
SELECT COALESCE(column1, '') || 'text';
SELECT UPPER(COALESCE(column1, ''));
```

---

**Next:** Try practice problems in `03-practice-problems.md`!
