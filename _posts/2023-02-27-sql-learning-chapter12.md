---
layout:     post
title:      "SQL learning - chapter12"
date:       2023-02-27
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第12课 联结表

这一课会介绍什么是联结，为什么使用联结，如何编写使用联结的 SELECT 语句。

## 12.1 联结

关键字 `join`，是 `SQL` `SELECT` 能执行的最重要的操作

### 12.1.1 关系表

一类数据一个表，如果存在相同的信息就要把信息分解成多个表。各表通过某些共同的值互相关联。

### 12.1.2 为什么使用联结

联结是一种机制，用来在一条 SELECT 语句中关联表，因此称为联结。可以联结多个表返回一组输出，联结在运行时关联表中正确的行。

## 12.2 创建联结

输入

```sql
SELECT vend_name, prod_name, prod_price
FROM Vendors, Products
WHERE Vendors.vend_id = Products.vend_id;
```

输出

```sql
vend_name prod_name prod_price
-------------------------------
Anvils R Us	.5 ton anvil	5.99
Anvils R Us	1 ton anvil	9.99
Anvils R Us	2 ton anvil	14.99
LT Supplies	Fuses	3.42
LT Supplies	Oil can	8.99
ACME	Detonator	13.00
ACME	Bird seed	10.00
ACME	Carrots	2.50
ACME	Safe	50.00
ACME	Sling	4.49
ACME	TNT (1 stick)	2.50
ACME	TNT (5 sticks)	10.00
Jet Set	JetPack 1000	35.00
Jet Set	JetPack 2000	55.00
```

一条 SELECT 语句反悔了两个不同表中的数据。

### 12.2.1 WHERE 子句的重要性

没有 WHERE 子句，第一个表的每一行会跟第二个表中的每一行进行配对，即使它们没有关系。

### 12.2.2 内联结

输入

```sql
SELECT vend_name, prod_name, prod_price
FROM Vendors INNER JOIN Products
ON Vendors.vend_id = Products.vend_id;
```

结果输出跟 12.2 的输出结果一致。

使用关键字 `INNER` `JOIN` 去联结。

### 12.2.3 联结多个表

输入

```sql
SELECT prod_name, vend_name, prod_price, quantity
FROM OrderItems, Products, Vendors
WHERE Products.vend_id = Vendors.vend_id
AND OrderItems.prod_id = Products.prod_id
AND order_num = 20009;
```

输出

```sql
prod_name vend_name prod_price quantity
-----------------------------------------
Bird seed	ACME	10.00	1
Oil can	LT Supplies	8.99	1
Sling	ACME	4.49	1
2 ton anvil	Anvils R Us	14.99	1
```

`SQL` 不限制 `SELECT` 语句中可以联结表的数量。

## 12.3 小结

联结是 `SQL` 中一个重要、最强大的特性，有效地使用联结需要对关系数据库有一定的了解。
