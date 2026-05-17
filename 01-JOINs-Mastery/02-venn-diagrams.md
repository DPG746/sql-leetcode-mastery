# 02: JOINs - Visual Venn Diagrams

## Understanding JOINs Visually

Think of each table as a circle. The overlap represents matching rows.

---

## INNER JOIN Venn Diagram

```
    TABLE A              TABLE B
    (Left)               (Right)
    
    ┌─────┐          ┌─────┐
    │ 1   │          │  2  │
    │ 2   │    ╭──╮  │  3  │
    │ 3   │    │██│  │  4  │
    │     │────╯██╰──│     │
    │     │    │██│  │     │
    └─────┘    ╰──╯  └─────┘
    
    INNER JOIN Result: [2, 3]
    (Only the overlapping shaded area)
```

**What it means:**
- Returns ONLY rows where values exist in BOTH tables
- If Table A has IDs [1,2,3] and Table B has [2,3,4]
- Result is [2,3] — only the common ones

**SQL:**
```sql
SELECT * FROM table_a 
INNER JOIN table_b ON table_a.id = table_b.id;
```

---

## LEFT JOIN Venn Diagram

```
    TABLE A              TABLE B
    (Left)               (Right)
    
    ┌─────┐          ┌─────┐
    │ 1   │          │  2  │
    │██2██│    ╭──╮  │  3  │
    │██3██│    │██│  │  4  │
    │     │────╯██╰──│     │
    │     │    │██│  │     │
    └─────┘    ╰──╯  └─────┘
    
    LEFT JOIN Result: [1, 2, 3]
    (Entire left circle + overlap)
```

**What it means:**
- Returns ALL rows from left table
- Plus any matching rows from right table
- Non-matching rows from left get NULL for right columns
- If Table A has [1,2,3] and Table B has [2,3,4]
- Result is [1,2,3] — everything from A

**SQL:**
```sql
SELECT * FROM table_a 
LEFT JOIN table_b ON table_a.id = table_b.id;
```

**Result breakdown:**
```
ID (from A) | Value (from B)
1           | NULL          ← No match in B
2           | (B's data)    ← Match found
3           | (B's data)    ← Match found
```

---

## RIGHT JOIN Venn Diagram

```
    TABLE A              TABLE B
    (Left)               (Right)
    
    ┌─────┐          ┌─────┐
    │ 1   │          │  2  │
    │ 2   │    ╭──╮  │██3██│
    │ 3   │    │██│  │██4██│
    │     │────╯██╰──│     │
    │     │    │██│  │     │
    └─────┘    ╰──╯  └─────┘
    
    RIGHT JOIN Result: [2, 3, 4]
    (Overlap + entire right circle)
```

**What it means:**
- Returns ALL rows from right table
- Plus any matching rows from left table
- Non-matching rows from right get NULL for left columns
- If Table A has [1,2,3] and Table B has [2,3,4]
- Result is [2,3,4] — everything from B

**SQL:**
```sql
SELECT * FROM table_a 
RIGHT JOIN table_b ON table_a.id = table_b.id;
```

---

## FULL OUTER JOIN Venn Diagram

```
    TABLE A              TABLE B
    (Left)               (Right)
    
    ┌─────┐          ┌─────┐
    │██1██│          │██2██│
    │██2██│    ╭──╮  │██3██│
    │██3██│    │██│  │██4██│
    │     │────╯██╰──│     │
    │     │    │██│  │     │
    └─────┘    ╰──╯  └─────┘
    
    FULL OUTER JOIN Result: [1, 2, 3, 4]
    (Entire left circle + entire right circle)
```

**What it means:**
- Returns ALL rows from BOTH tables
- Rows that don't match get NULL values
- If Table A has [1,2,3] and Table B has [2,3,4]
- Result is [1,2,3,4] — everything from both

**SQL:**
```sql
SELECT * FROM table_a 
FULL OUTER JOIN table_b ON table_a.id = table_b.id;
```

**Result breakdown:**
```
A_ID | B_ID | A_Value | B_Value
1    | NULL | A       | NULL    ← In A only
2    | 2    | B       | B       ← In both
3    | 3    | C       | C       ← In both
NULL | 4    | NULL    | D       ← In B only
```

---

## CROSS JOIN - No Overlap!

```
    TABLE A              TABLE B
    (Left)               (Right)
    
    ┌───────┐           ┌───────┐
    │ 1, 2  │           │ A, B  │
    │ 3     │           │ C     │
    └───────┘           └───────┘
    
    No ON condition - creates EVERY combination
    
    CROSS JOIN Result:
    (1,A), (1,B), (1,C),
    (2,A), (2,B), (2,C),
    (3,A), (3,B), (3,C)
    
    Total: 3 × 3 = 9 rows!
```

**What it means:**
- No relationship between tables
- Every row from Table A combined with every row from Table B
- Result size = (rows in A) × (rows in B)

**SQL:**
```sql
SELECT * FROM table_a 
CROSS JOIN table_b;
-- No ON clause!
```

---

## Practical Example: JOIN Types in Action

### Sample Data:
```
EMPLOYEES:
emp_id | name
1      | Alice
2      | Bob
3      | Charlie

DEPARTMENTS:
dept_id | dept_name
10      | Sales
20      | IT
30      | HR
```

### With ON condition: `employees.emp_id = departments.dept_id`

This is a weird condition (emp_id != dept_id), so nothing matches. Let me use realistic data instead:

### Realistic Sample:
```
EMPLOYEES:
emp_id | name     | dept_id
1      | Alice    | 10
2      | Bob      | 20
3      | Charlie  | NULL

DEPARTMENTS:
dept_id | dept_name
10      | Sales
20      | IT
30      | HR
```

**INNER JOIN on emp.dept_id = dept.dept_id:**
```
Result:
emp_id | name    | dept_id | dept_name
1      | Alice   | 10      | Sales
2      | Bob     | 20      | IT
(Charlie excluded - no dept_id)
```

**LEFT JOIN on emp.dept_id = dept.dept_id:**
```
Result:
emp_id | name    | dept_id | dept_name
1      | Alice   | 10      | Sales
2      | Bob     | 20      | IT
3      | Charlie | NULL    | NULL
(Charlie included with NULLs)
```

**RIGHT JOIN on emp.dept_id = dept.dept_id:**
```
Result:
emp_id | name    | dept_id | dept_name
1      | Alice   | 10      | Sales
2      | Bob     | 20      | IT
NULL   | NULL    | 30      | HR
(HR department included even with no employee)
```

**FULL OUTER JOIN on emp.dept_id = dept.dept_id:**
```
Result:
emp_id | name    | dept_id | dept_name
1      | Alice   | 10      | Sales
2      | Bob     | 20      | IT
3      | Charlie | NULL    | NULL
NULL   | NULL    | 30      | HR
(Everyone and everything!)
```

**CROSS JOIN:**
```
Result (9 rows):
emp_id | name    | dept_id | dept_name
1      | Alice   | 10      | Sales
1      | Alice   | 20      | IT
1      | Alice   | 30      | HR
2      | Bob     | 10      | Sales
2      | Bob     | 20      | IT
2      | Bob     | 30      | HR
3      | Charlie | 10      | Sales
3      | Charlie | 20      | IT
3      | Charlie | 30      | HR
(All combinations!)
```

---

## Summary: Which Circle(s) Light Up?

| JOIN Type | Left Circle | Overlap | Right Circle | Total |
|-----------|------------|---------|-------------|-------|
| INNER     | ✗ | ✓ | ✗ | Matches only |
| LEFT      | ✓ | ✓ | ✗ | All left + matches |
| RIGHT     | ✗ | ✓ | ✓ | Matches + all right |
| FULL      | ✓ | ✓ | ✓ | Everything |
| CROSS     | N/A | N/A | N/A | All combinations |

---

## 🧠 Mental Model

Think of JOINs like a party invitation system:

- **INNER JOIN:** Only people invited by BOTH party A and party B
- **LEFT JOIN:** Everyone invited by party A, plus anyone also invited by B
- **RIGHT JOIN:** Everyone invited by party B, plus anyone also invited by A
- **FULL OUTER:** Everyone invited by EITHER party A or party B (or both)
- **CROSS JOIN:** Everyone from party A paired with everyone from party B (awkward!)

---

Next: Go to `03-practice-problems.md` to practice!
