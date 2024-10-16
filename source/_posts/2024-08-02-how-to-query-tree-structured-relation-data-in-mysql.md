---
title: How to query tree-structured relation data in MySQL
date: 2024-08-02 10:08:52
tags: [MySQL]
categories: Database
---

To query hierarchical relational data in `MySQL`, `recursive Common Table Expressions (CTEs)` are typically used. However, MySQL did not support recursive CTEs before version 8.0, so in earlier versions, `self-joins` are commonly used to handle such queries. Below is an example using a self-join, assuming we have a table employees that contains information about employees and their manager IDs (manager_id).

## Create Table and Insert Data
Creating a table named `employees` with the following columns:

```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    manager_id INT,
    FOREIGN KEY (manager_id) REFERENCES employees(id)
);

INSERT INTO employees (id, name, manager_id) VALUES
(1, 'CEO', NULL),
(2, 'CTO', 1),
(3, 'CFO', 1),
(4, 'Developer Lead', 2),
(5, 'Accountant Lead', 3),
(6, 'Developer', 4),
(7, 'Junior Developer', 4),
(8, 'Senior Accountant', 5),
(9, 'Junior Accountant', 5);
```

## Self-Join
We can search for all employees and their direct reports (subordinates) using a self-join. The following SQL statement will list all employees and their direct manager's name.

```sql
SELECT e1.name AS employee_name, e2.name AS manager_name
FROM employees e1
LEFT JOIN employees e2 ON e1.manager_id = e2.id
ORDER BY e1.manager_id, e1.id;
```

We can also use a self-join to count the number of direct reports for each manager. The following SQL statement will list all managers and the number of their direct reports.

```sql
SELECT e1.name AS manager_name, COUNT(e2.id) AS subordinate_count
FROM employees e1
LEFT JOIN employees e2 ON e1.id = e2.manager_id
GROUP BY e1.id
ORDER BY subordinate_count DESC;
```

## Recursive Common Table Expressions (CTEs)

MySQL 8.0 introduced support for recursive CTEs, which allows us to query hierarchical relational data more efficiently. The following SQL statement will list all employees and their direct reports (subordinates) using a recursive CTE.

```sql
WITH RECURSIVE subordinates AS (
    SELECT id, name, manager_id
    FROM employees
    WHERE id = 1 -- root node CEO, we can replace with any other root node ID, for example 2 which is CTO
    UNION ALL
    SELECT e.id, e.name, e.manager_id
    FROM employees e
    INNER JOIN subordinates s ON e.manager_id = s.id
)
SELECT * FROM subordinates;
```

But please note that this method only works for `MySQL 8.0` and above, as these versions support `recursive CTEs`.