# 03: JOINs Practice Problems

**Difficulty Guide:**
- 🟢 Easy: Basic single JOIN with simple conditions
- 🟡 Medium: Multiple JOINs or complex conditions
- 🔴 Hard: Advanced multi-table scenarios

---

## Problem 1.1: Simple INNER JOIN (Easy) 🟢

**Scenario:** You work at an HR company and need to create a report showing each employee with their department name.

**Question:** Show all employees and their department names. Only include employees who have a department assigned.

**Sample Data:**
```
employees table:
emp_id  emp_name      dept_id
101     Alice         1
102     Bob           2
103     Charlie       1
104     Diana         NULL

departments table:
dept_id  dept_name
1        Sales
2        IT
3        HR
```

**Expected Output:**
```
emp_id  emp_name   dept_id  dept_name
101     Alice      1        Sales
102     Bob        2        IT
103     Charlie    1        Sales
```

**Hint:** 
- Which JOIN keeps only matching records? 
- Look for the relationship between employees and departments tables
- Check the department ID columns

---

## Problem 1.2: LEFT JOIN with NULL Handling (Easy) 🟢

**Scenario:** Your manager wants to see ALL employees, even those without a department assignment, so HR can follow up with them.

**Question:** Show all employees and their department names. If an employee has no department, still show them with NULL for department name.

**Sample Data:** (Same as Problem 1.1)

**Expected Output:**
```
emp_id  emp_name   dept_name
101     Alice      Sales
102     Bob        IT
103     Charlie    Sales
104     Diana      NULL
```

**Hint:**
- Which JOIN keeps all rows from the left table?
- Notice Diana appears even though she has no department
- What column in employees connects to departments?

---

## Problem 1.3: Multiple JOINs (Medium) 🟡

**Scenario:** Create a comprehensive report for management showing which employee completed which order from which customer department.

**Question:** Show the customer name, order ID, employee name, and the employee's department name for each order. Include all orders even if the employee's department info is missing.

**Sample Data:**
```
customers:
customer_id  customer_name
1            Acme Corp
2            Beta Inc

orders:
order_id  customer_id  emp_id
1001      1            101
1002      2            102

employees:
emp_id   emp_name     dept_id
101      Alice        1
102      Bob          2

departments:
dept_id  dept_name
1        Sales
2        IT
```

**Expected Output:**
```
customer_name  order_id  emp_name  dept_name
Acme Corp      1001      Alice     Sales
Beta Inc       1002      Bob       IT
```

**Hint:**
- Start with one relationship and build step by step
- First join customers with orders
- Then join with employees
- Finally join with departments
- Think about join order: Does it matter?

---

## Problem 1.4: Self-JOIN (Medium) 🟡

**Scenario:** Employees have managers (who are also employees). Create a report showing each employee and their manager's name.

**Question:** Show each employee's name alongside their manager's name. If an employee has no manager, show NULL for manager name.

**Sample Data:**
```
employees:
emp_id  emp_name      manager_id
101     Alice         NULL
102     Bob           101
103     Charlie       101
104     Diana         102
```

**Expected Output:**
```
emp_name    manager_name
Alice       NULL
Bob         Alice
Charlie     Alice
Diana       Bob
```

**Hint:**
- This is a special case where you join a table with itself
- Use aliases: one for employees, one for managers
- Example: `FROM employees e LEFT JOIN employees m ON e.manager_id = m.emp_id`

---

## Problem 1.5: CROSS JOIN Practical Use (Medium) 🟡

**Scenario:** Your company is planning to assign all salespeople to all regions for a training program. You need to see all possible combinations.

**Question:** Create a list of all possible salesperson-region combinations for training assignments.

**Sample Data:**
```
salespeople:
salesperson_id  salesperson_name
201             John
202             Jane

regions:
region_id  region_name
1          North
2          South
3          East
```

**Expected Output:**
```
salesperson_name  region_name
John              North
John              South
John              East
Jane              North
Jane              South
Jane              East
```

**Hint:**
- CROSS JOIN creates all combinations
- No ON clause needed
- 2 salespeople × 3 regions = 6 rows

---

## 📝 How to Approach These Problems

1. **Read carefully** - Understand what tables are involved
2. **Identify relationships** - Which columns connect the tables?
3. **Choose the right JOIN** - INNER? LEFT? CROSS?
4. **Write step by step** - Test each JOIN separately if needed
5. **Check the output** - Does it match expected results?

---

## 🆘 Need Help?

**If stuck on a problem:**
1. Re-read the hint
2. Look back at the concept file (01-concept.md)
3. Review the Venn diagrams (02-venn-diagrams.md)
4. Check if you're using the correct JOIN type
5. Then look at the solution in 04-solutions.md

---

## ✅ Ready to Solve?

Try each problem and write your query. Don't look at solutions until you've attempted them!

**Next:** Go to `04-solutions.md` once you've solved these or need help.
