---
title: MySQL 8.x CTEs feature - WITH clause
date: 2024-07-26 09:19:59
tags: [MySQL]
categories: Database
---

MySQL `Common Table Expressions (CTEs)` are a powerful feature introduced in `MySQL 8.0`. CTEs are a type of MySQL 8.0 that provide a way to create `temporary result sets` that can be referenced within a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statement. {% pbg danger, The primary purpose of `CTEs` is to make complex queries more readable and manageable by breaking them down into simpler, more modular parts %}.

{% image /assets/images/mysql/mysql-cte-with-clause.png, width=600px, alt=MYSQL CTEs feature - WITH clause %}

## Purpose of CTEs

- **Readability**: `CTEs` can make SQL queries more readable, especially for complex queries involving multiple subqueries or recursive operations.
- **Modularity**: They allow you to define a temporary result set that can be reused within the same query, promoting code reuse and reducing redundancy.
- **Recursive Queries**: CTEs support recursive queries, which are useful for querying hierarchical data like organizational charts, bill of materials, or tree structures.

## How to Use CTEs
`CTEs` are defined using the `WITH` clause and can be referenced within the main query. Here’s the basic syntax:

``` sql
WITH cte_name AS (
    SELECT ...
)

SELECT ... FROM cte_name;
``` 

### Basic CTE
Suppose you have a table employees and you want to find the average salary of employees in each department.

``` sql
WITH DepartmentSalaries AS (
    SELECT department_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
)

SELECT * FROM DepartmentSalaries;
```

In this example, DepartmentSalaries is a CTE that calculates the average salary for each department. The main query then selects from this CTE.

`CTEs` feature also supports multiple temporary result sets in the same query, see below example:

``` sql
WITH
  cte1 AS (SELECT a, b FROM table1),
  cte2 AS (SELECT c, d FROM table2)

SELECT b, d FROM cte1 JOIN cte2 WHERE cte1.a = cte2.c;
```

Above sqls defines two CTEs cte1 and cte2 and then joins them using a WHERE clause.

### Recursive CTE
A CTE can refer to itself to define a `recursive CTE`. Common applications of recursive CTEs include series generation and traversal of hierarchical or tree-structured data.

Suppose you have a table employees with a self-referencing column manager_id to represent a hierarchy. You want to find all subordinates of a given manager.

``` sql
WITH RECURSIVE Subordinates AS (
    SELECT employee_id, manager_id, name
    FROM employees
    WHERE manager_id = 1  -- Starting with the manager ID 1
    UNION ALL
    SELECT e.employee_id, e.manager_id, e.name
    FROM employees e
    INNER JOIN Subordinates s ON e.manager_id = s.employee_id
)

SELECT * FROM Subordinates;
```

In this example, Subordinates is a recursive CTE that starts with employees directly reporting to manager ID 1 and then recursively includes all their subordinates.

## Key Points
- **Non-Recursive CTEs**: These are straightforward and do not involve recursion. They are simply a way to define a temporary result set for use within the query.
- **Recursive CTEs**: These involve recursion and are useful for hierarchical or tree-structured data. They must include a `UNION ALL` clause to combine the initial result set with the recursive result set.
- **Scope**: CTEs are scoped to the query they are defined in and cannot be referenced outside of that query.

`CTEs` are a powerful tool in MySQL that can significantly improve the `readability` and `maintainability` of `complex SQL queries`.

## Reference Links:
For other scenarios, like use `WITH` clause in `UPDATE` or `DELETE` statements, please refer to the following links:
- **WITH (Common Table Expressions)**: https://dev.mysql.com/doc/refman/8.4/en/with.html