---
layout:     post
title:      "SQL learning - chapter9"
date:       2023-02-24
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第9课 汇总数据

这一课介绍什么事 SQL 的聚集函数，如何利用它们汇总表的数据

## 9.1 汇集函数

- 确定表中的行数
- 获得表中某些行的和
- 找出表列的最大值、最小值、平均值

SQL 聚集函数

|函数|说明|
|---|---|
| AVG() | 返回某列的平均值 |
| COUNT() | 返回某列的行数 |
| MAX() | 返回某列的最大值 |
| MIN() | 返回某列的最小值 |
| SUM() | 返回某列的和 |

### 9.1.1 AVG() 函数

通过对表中行数计算并计算其列值之和，求得该列的平均值。

输入

```sql
SELECT AVG(prod_price) AS avg_price
FROM Products;
```

输出

```sql
avg_price
---------
16.133571
```

返回某一列的平均值，可以这样写

输入

```sql
SELECT AVG(prod_price) AS avg_price
FROM Products
WHERE vend_id = 1001;
```

输出

```sql
avg_price
----------
10.323333
```

### 9.1.2 COUNT() 函数

计算表中行的数目或特定条件的数目。

- 使用 COUNT(*) 对表中的行进行计算，不忽略 `NULL` 值

- 使用 COUNT(column) 对特定列中具有值的行进行计数，忽略 `NULL` 值

输入

```sql
SELECT COUNT(*) AS num_cust
FROM Customers;
```

输出

```sql
num_cust
---------
5
```

输入

```sql
SELECT COUNT(cust_email) AS num_cust
FROM Customers;
```

输出

```sql
num_cust
---------
3
```

## 9.2 聚集不同值

- 对所有行执行计算，指定 `ALL` 参数或不指定参数
- 只包含不同的值，指定 `DISTRINCT` 参数

输入

```sql
SELECT AVG(DISTINCT(prod_price)) AS avg_price
FROM Products
WHERE vend_id = 1001;
```

输出

```sql
avg_price
----------
10.323333
```

## 9.3 组合聚集函数

SELECT 语句可根据需要包含多个聚集函数。

输入

```sql
SELECT COUNT(*) AS num_items,
  MAX(prod_price) AS max_price,
  AVG(prod_price) AS avg_price,
  MIN(prod_price) AS min_price
FROM Products;
```

输出

```sql
num_items, max_price, avg_price, min_price
--------------------------------------------
14	55.00	16.133571	2.50
```

## 9.4 小结

聚集函数用来汇总数据。SQL 支持 5 个聚集函数，可以使用多种方法使用它们，返回所需的结果。
