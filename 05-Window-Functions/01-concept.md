# 05: Window Functions - Core Concepts

## What are Window Functions?

**Window functions** perform calculations across related rows without collapsing them into groups.

**Comparison:**
```sql
-- GROUP BY collapses to one row per group
SELECT dept, COUNT(*) FROM employees GROUP BY dept;

-- Window function keeps all rows
SELECT dept, COUNT(*) OVER (PARTITION BY dept) FROM employees;
```

**Analogy:** Think of a "window" or "frame" of rows. The function operates on rows in that window.

---

## Window Function Syntax

```sql
SELECT 
    column1,
    window_function() OVER (
        [PARTITION BY column2]
        [ORDER BY column3]
        [ROWS BETWEEN ... AND ...]
    )
FROM table;
```

---

## Main Window Functions

### 1. Ranking Functions

#### ROW_NUMBER()
```sql
SELECT 
    emp_name,
    salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) as rank
FROM employees;
```

Returns: 1, 2, 3, 4, 5... (unique for ties)

#### RANK()
```sql
SELECT 
    emp_name,
    salary,
    RANK() OVER (ORDER BY salary DESC) as rank
FROM employees;
```

Returns: 1, 2, 2, 4, 5... (skips after ties)

#### DENSE_RANK()
```sql
SELECT 
    emp_name,
    salary,
    DENSE_RANK() OVER (ORDER BY salary DESC) as rank
FROM employees;
```

Returns: 1, 2, 2, 3, 4... (no skips)

**Example with data:**
```
emp_name | salary | ROW_NUMBER | RANK | DENSE_RANK
Alice    | 100000 | 1          | 1    | 1
Bob      | 100000 | 2          | 1    | 1
Charlie  | 90000  | 3          | 3    | 2
Diana    | 90000  | 4          | 3    | 2
Eve      | 80000  | 5          | 5    | 3
```

---

### 2. Aggregate Window Functions

#### SUM OVER (Running total)
```sql
SELECT 
    emp_name,
    salary,
    SUM(salary) OVER (ORDER BY emp_id) as running_total
FROM employees
ORDER BY emp_id;
```

#### AVG OVER
```sql
SELECT 
    emp_name,
    salary,
    AVG(salary) OVER (PARTITION BY dept_id) as dept_avg
FROM employees;
```

---

### 3. Offset Functions

#### LAG() - Access previous row
```sql
SELECT 
    order_id,
    order_date,
    LAG(order_date) OVER (ORDER BY order_date) as prev_order_date
FROM orders;
```

#### LEAD() - Access next row
```sql
SELECT 
    order_id,
    order_date,
    LEAD(order_date) OVER (ORDER BY order_date) as next_order_date
FROM orders;
```

---

## PARTITION BY vs ORDER BY

### PARTITION BY (optional)
- Divides rows into groups
- Function operates independently per group

```sql
-- Show each employee's rank within their department
SELECT 
    dept_id,
    emp_name,
    salary,
    ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) as dept_rank
FROM employees;
```

### ORDER BY (for ranking functions)
- Determines sort order for ranking
- Determines order for LAG/LEAD
- Determines order for running totals

---

## Practical Examples

### Example 1: Top 2 employees per department
```sql
WITH ranked_emp AS (
    SELECT 
        dept_id,
        emp_name,
        salary,
        ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) as rnk
    FROM employees
)
SELECT * FROM ranked_emp WHERE rnk <= 2;
```

### Example 2: Compare to previous order
```sql
SELECT 
    emp_id,
    order_id,
    order_date,
    LAG(order_date) OVER (PARTITION BY emp_id ORDER BY order_date) as prev_order,
    order_date - LAG(order_date) OVER (PARTITION BY emp_id ORDER BY order_date) as days_between
FROM orders;
```

### Example 3: Running balance
```sql
SELECT 
    account_id,
    transaction_date,
    amount,
    SUM(amount) OVER (PARTITION BY account_id ORDER BY transaction_date) as running_balance
FROM transactions;
```

---

## Common Mistakes

### ❌ Mistake 1: Missing PARTITION BY when needed
```sql
-- WRONG: Total sum, not per department
SELECT dept_id, SUM(salary) OVER (ORDER BY emp_id)
FROM employees;
```

### ✅ Fix:
```sql
SELECT dept_id, SUM(salary) OVER (PARTITION BY dept_id ORDER BY emp_id)
FROM employees;
```

### ❌ Mistake 2: Using window function in WHERE
```sql
SELECT * FROM employees 
WHERE ROW_NUMBER() OVER (ORDER BY salary DESC) <= 10;
-- ERROR! Can't use window functions in WHERE
```

### ✅ Fix: Use CTE or subquery
```sql
WITH ranked AS (
    SELECT *, ROW_NUMBER() OVER (ORDER BY salary DESC) as rnk
    FROM employees
)
SELECT * FROM ranked WHERE rnk <= 10;
```

---

## When to Use Each Ranking Function

| Function | Use When |
|----------|----------|
| ROW_NUMBER() | Need unique row numbers (gaps OK) |
| RANK() | Need ranking with gaps for ties |
| DENSE_RANK() | Need ranking without gaps |

---

## Window Frame (Advanced)

```sql
SELECT 
    emp_name,
    salary,
    AVG(salary) OVER (
        ORDER BY emp_id 
        ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING
    ) as moving_avg
FROM employees;
```

---

**Next:** Try `03-practice-problems.md` to practice window functions!
