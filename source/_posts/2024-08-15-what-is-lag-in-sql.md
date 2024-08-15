---
title: 详解 SQL 中的 LAG 函数
date: 2024-08-15 09:24:21
categories: [Database]
tags: [SQL, MySQL, Big Data]
---

最近在做数据分析，需要挖掘数据随时间变化的信息。所有数据物理存储在AWS S3上，通过AWS Glue Catalog和AWS Athena进行数据查询。AWS Athena支持SQL语言，可以对数据进行分析。在处理时间序列数据或分析行间变化时， SQL中的 `LAG` 函数和 `LEAD` 函数是非常有用的。下面，我们来看一下 LAG 函数的基本用法。

## 什么是 LAG 函数？
在 SQL 中，`LAG` 函数是一种`窗口函数`，{% pbg warning, 用于获取当前行之前某一行的值。这在处理时间序列数据或分析行间变化时非常有用。LAG 函数可以让你访问当前行之前的行数据，而不需要使用子查询或自连接。%} 而`LEAD` 函数则是获取当前行之后的行数据。他们的语法和用法类似，只是方向不同。

## LAG 函数的语法

``` sql
LAG(expression, offset, default) OVER (PARTITION BY partition_column ORDER BY order_column)
```

- `expression`：要返回的列或计算结果。
- `offset`：向前查找的行数，默认为 1（即前一行）。
- `default`：当没有前行时返回的默认值，默认为 NULL。
- `PARTITION BY`：用于将数据分组。如果省略，LAG 会在整个结果集上应用。
- `ORDER BY`：确定行的顺序，LAG 函数会根据这个顺序来访问前面的行。

## 如何使用 LAG 函数
`LAG` 函数在使用时通常与 `OVER` 子句一起使用。OVER 子句用于定义窗口（即应用 LAG 函数的范围）。在窗口中，`ORDER BY` 确定了行的顺序，`PARTITION BY` 则可以用来将数据分组，使每个分组内的计算互相独立。

### 基本使用示例：

假设我们有一个名为 sales 的表，包含 sale_date 和 amount 列。我们想要比较每笔销售金额与前一笔销售金额的变化。

``` sql
SELECT
    sale_date,
    amount,
    LAG(amount, 1) OVER (ORDER BY sale_date) AS previous_amount
FROM sales;
```

在这个查询中：

amount 是当前销售金额。
LAG(amount, 1) 获取当前销售的前一笔销售金额（根据 sale_date 排序）。

## 使用场景
1. 时间序列分析：
LAG 函数非常适合分析时间序列数据，帮助用户了解数据变化趋势。例如，分析每月的销售数据，找出增长或下降的趋势。

2. 计算变化量：
可以计算当前值与前一值之间的变化量，例如销售额变化、温度变化等。

3. 生成滚动报告：
LAG 可以用来生成带有前值的滚动报告，例如计算累计销售额，或者生成滞后数据用于报表。

## 举个例子
假设我们有一个销售记录表 monthly_sales，结构如下：

``` sql
CREATE TABLE monthly_sales (
    month DATE,
    sales_amount DECIMAL(10, 2)
);

INSERT INTO monthly_sales (month, sales_amount) VALUES
('2024-01-01', 1000.00),
('2024-02-01', 1500.00),
('2024-03-01', 1200.00),
('2024-04-01', 1700.00);
```

我们可以使用 `LAG` 函数来比较每个月的销售额与前一个月的销售额：

```sql
SELECT
    month,
    sales_amount,
    LAG(sales_amount, 1) OVER (ORDER BY month) AS previous_month_sales,
    sales_amount - LAG(sales_amount, 1) OVER (ORDER BY month) AS sales_difference
FROM monthly_sales;
```

查询结果：

| month      | sales_amount | previous_month_sales | sales_difference |
|------------|--------------|----------------------|------------------|
| 2024-01-01 | 1000.00      | NULL                 | NULL             |
| 2024-02-01 | 1500.00      | 1000.00              | 500.00           |
| 2024-03-01 | 1200.00      | 1500.00              | -300.00          |
| 2024-04-01 | 1700.00      | 1200.00              | 500.00           |


在这个查询中：
previous_month_sales 显示了前一个月的销售额。
sales_difference 显示了当前月与前一个月的销售额差异。
通过上述查询，我们可以方便地分析销售数据的变化情况。

## 总结
`LAG` 函数是一个强大的工具，可以帮助你在数据分析中处理行间的比较和变化。无论是用于时间序列数据、计算变化量，还是生成滚动报告，LAG 函数都能提供有价值的信息。它通常与 `OVER` 子句一起使用, 在OVER子句中，我们可以指定分组条件、排序条件等。`ORDER BY` 确定行的顺序，`PARTITION BY` 则可以用来将数据分组，使每个分组内的计算互相独立。
