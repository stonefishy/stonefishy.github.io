---
title: SQL 中的 IS DISTINCT FROM 语法详解
date: 2024-08-16 09:22:15
categories: [Database]
tags: [SQL, MySQL, Big Data]
---


在SQL查询中，比较操作符 `=` 通常用于检查两个值是否相等。然而，当涉及到处理缺失值（`NULL`）时，这种操作符就会面临挑战。为了解决这一问题，{% pbg green, SQL 提供了 `IS DISTINCT FROM` 操作符，它用于精确比较两个值是否不同，即使这些值中有 NULL, 可以确保结果的可靠性 %}。本文将详细介绍 IS DISTINCT FROM 的语法、解决的问题以及常见的使用场景。

## 语法
IS DISTINCT FROM 的基本语法如下：

``` sql
expression1 IS DISTINCT FROM expression2
```

其中 expression1 和 expression2 是要进行比较的两个表达式。 该操作符返回布尔值：TRUE、FALSE。

## 主要解决的问题
在SQL中，`NULL` 值代表缺失或未知的数据。当两个表达式中至少有一个为 NULL 时，使用传统的比较操作符（如 = 或 <>）进行比较会导致不确定的结果。具体来说：

- expression1 = expression2 在 expression1 或 expression2 为 NULL 时会返回 UNKNOWN。
- expression1 <> expression2 在 expression1 或 expression2 为 NULL 时也会返回 UNKNOWN。

比如下面的查询语句：

``` sql
select 
    1 = NULL as A1,
    NULL <> 1 as A2,
    NULL = NULL as A3,
    NULL <> NULL as A4,
    1 = 1 as A5, 
    1 <> 1 as A6
```

会返回以下结果：

A1 | A2 | A3 | A4 | A5 | A6
--- | --- | --- | --- | --- | ---
 |  |  |  |  | TRUE | FALSE


可以看到，当 expression1 或 expression2 为 NULL 时，传统的比较操作符会返回 UNKNOWN空值， 如上面的A1, A2, A3, A4的结果值，这就会导致不确定性。

`IS DISTINCT FROM` 操作符的出现，解决了这些问题。它能正确处理 `NULL` 值，会返回 `TRUE 或 FALSE`，确保结果的可靠性。 在以下情况下返回 TRUE：
- expression1 和 expression2 都为 NULL。
- expression1 和 expression2 的值不同（不论是否为 NULL）。

而在 expression1 和 expression2 相等（包括都是 NULL）的情况下，IS DISTINCT FROM 返回 FALSE。 另外还有一个 `IS NOT DISTINCT FROM` 操作符，用于判断两个值是否相等。其用法一样，只是语义相反。

下面的例子查询：

``` sql
SELECT 
    1 IS DISTINCT FROM NULL as B1,
    NULL IS DISTINCT FROM 1 as B2,
    1 IS DISTINCT FROM 2 as B3,
    NULL IS DISTINCT FROM NULL as B4,
    1 IS DISTINCT FROM 1 as B5,
    1 IS NOT DISTINCT FROM 1 as B6
```

查询结果如下:

B1  | B2  | B3  | B4  | B5   | B6
--- | ---  | --- | --- | ---  | ---
TRUE | TRUE | TRUE | FALSE | FALSE | TRUE

可以看到，IS DISTINCT FROM 正确处理 NULL 值，返回 TRUE 或 FALSE，确保结果的可靠性。

## 使用场景

### 数据清洗和验证

在`数据清洗`和`数据验证`过程中，经常需要检查数据库中的值是否不同，包括对 NULL 值的处理。例如，比较用户输入的数据与现有记录，以确定是否有不同的记录。{% pbg warning, 使用 IS DISTINCT FROM 可以更准确地处理 NULL 值，避免出现错误或遗漏。%}

``` sql
SELECT *
FROM users
WHERE username IS DISTINCT FROM 'andrewsy';
```

这条查询会返回所有 username 与 'andrewsy' 不同的记录，包括那些 username 为 NULL 的记录。

### 数据更新

在更新数据时，使用 IS DISTINCT FROM 可以确保只有在数据实际变化时才进行更新，从而避免不必要的更新操作。

``` sql
UPDATE users
SET email = 'new_andrewsy@email.com'
WHERE email IS DISTINCT FROM 'new_andrewsy@email.com';
```

这条查询会更新所有 email 不同于 'new_andrewsy@email.com' 的记录，包括那些 email 为 NULL 的记录。

### 数据比较

在进行复杂的数据比较时，尤其是涉及到 NULL 值时，IS DISTINCT FROM 提供了更直观的比较逻辑。例如，在合并两个数据集时，可以使用此操作符来确保唯一性。

``` sql
SELECT 
    *
FROM 
    dataset1
FULL OUTER JOIN 
    dataset2
ON 
    dataset1.id IS DISTINCT FROM dataset2.id
```

这条查询会找出两个数据集中 id 不同的记录，包括 id 为 NULL 的情况。

## 注意事项

`IS DISTINCT FROM` 是 SQL 标准中的一部分，但并非所有数据库系统都支持。具体的支持情况需要查阅数据库的文档。在使用 IS DISTINCT FROM 时，确保数据库系统的版本和文档中对此操作符的支持及行为一致。

## 总结
`IS DISTINCT FROM` 是一个强大的工具，用于在 SQL 中处理包含 NULL 值的数据比较。它解决了传统比较操作符在处理 NULL 值时的不足，使得数据验证、更新和比较更加准确和可靠。在实际应用中，根据数据库系统的支持情况，合理使用 IS DISTINCT FROM 可以显著提高数据操作的精确性和健壮性。
