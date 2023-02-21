---
layout:     post
title:      "SQL learning - chapter4"
date:       2023-02-19
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第4课 过滤数据

这一节课讲授如何使用 `SELECT` 语句的 `WHERE` 子句指定的搜索条件。

## 4.1 使用 WHERE 子句

根据特定操作或报告的需要提取数据的子集。只检索所需数据需要指定搜索条件（search criteria），搜索条件也称为过滤条件（filter condition）。

输入

```sql
SELECT prod_name, prod_price
FROM Products
WHERE prod_price = 5.59;
```

输出

```sql
prod_name prod_price
----------------------
.5 ton anvil 5.99
```

搜索出两列，但不返回所有行，只返回 prod_price 值为 5.99 的行。

## 4.2 WHERE 子句操作符

SQL 支持下列的所有条件操作符

| 操作符 | 说明  |
| ----  | ---- |
| =     | 等于  |
| <>    | 不等于 |
| !=    | 不等于 |
| <     | 小于 |
| <=    | 小于等于 |
| !<    | 不小于 |
| >     | 大于 |
| >=    | 大于等于 |
| !>    | 不大于 |
| BETWEEN| 在指定的两个值之间 |
| IS NULL| 为 NULL 值 |

### 4.2.1 检查单个值

列出所有加个小于10美元的产品

输入

```sql
SELECT prod_name, prod_price
FROM Products
WHERE prod_price < 10;
```

输出

```sql
prod_name prod_price
-----------------------
.5 ton anvil	5.99
1 ton anvil	9.99
Carrots	2.50
Fuses	3.42
Oil can	8.99
Sling	4.49
TNT (1 stick)	2.50
```

### 4.2.2 不匹配检查

输入

```sql
SELECT vend_id, prod_name
FROM Products
WHERE vend_id <> '1001';
```

输出

```sql
vend_id prod_name
------------------
1003	Detonator
1003	Bird seed
1003	Carrots
1002	Fuses
1005	JetPack 1000
1005	JetPack 2000
1002	Oil can
1003	Safe
1003	Sling
1003	TNT (1 stick)
1003	TNT (5 sticks)
```

注意，如果比较的字段是字符类型必须使用引号。

### 4.2.3 范围值检查

可以使用 `BETWEEN` 关键字来查询

输入

```sql
SELECT prod_name, prod_price
FROM Products
WHERE prod_price BETWEEN 5 AND 10;
```

输出

```sql
prod_name prod_price
------------------------
.5 ton anvil	5.99
1 ton anvil	9.99
Bird seed	10.00
Oil can	8.99
TNT (5 sticks)	10.00
```

### 4.2.4 空值检查

输入

```sql
SELECT prod_name
FROM Products
WHERE prod_price IS Null;
```

由于没有这样的行，所以没有返回数据。

输入

```sql
SELECT cust_name
FROM Customers
WHERE cust_email IS Null;
```

输出

```sql
cust_name
----------
Mouse House
E Fudd
```

## 4.3 小结

介绍了如何用 `SELECT` 语句的 `WHERE` 子句过滤返回的数据。学习了如何检索相等、不相等、小于、值的范围以及 NULL 值等。
