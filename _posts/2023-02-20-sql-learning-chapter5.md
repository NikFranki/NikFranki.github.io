---
layout:     post
title:      "SQL learning - chapter5"
date:       2023-02-20
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# 第5课 高级数据过滤

这一节课讲授如何组合 `WHERE` 子句以建立更强、更高级的搜索条件。学习如何使用 `NOT` 和 `IN` 操作符。

## 5.1 组合 WHERE 子句

第4节课介绍在过滤数据时都是使用单一的条件。为了进行更强的过滤控制，SQL 允许给出多个 `WHERE` 子句。哪些子句有两种使用方式，即以 `AND` 子句或 `OR` 子句的方式使用。

### 5.1.1 AND 操作符

要通过不止一个列进行过滤，可以使用 `AND` 关键字给 `WHERE` 子句附加条件。

输入

```sql
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id = '1001' AND prod_price <= 10;
```

输出

```sql
prod_id prod_price prod_name
ANV01	5.99	.5 ton anvil
ANV02	9.99	1 ton anvil
```

可以增加多个过滤条件，每个条件都需要 `AND` 关键字。

### 5.1.2 OR 操作符

`OR` 操作符与 `AND` 操作符刚好相反，它指示 DBMS 检索匹配任一条件的行。在满足第一个条件的情况下，就不会进行计算第二个条件。

输入

```sql
SELECT prod_name, prod_price
FROM Products
WHERE vend_id = 1001 OR vend_id = 1005;
```

输出

```sql
prod_name prod_price
-----------------------
.5 ton anvil	5.99
1 ton anvil	9.99
2 ton anvil	14.99
JetPack 1000	35.00
JetPack 2000	55.00
```

### 5.1.3 求值顺序

`WHERE` 子句可以包含任意数目的 `AND` 和 `OR` 操作符。允许两者结合进行复杂、高级的搜索。

输入

```sql
SELECT prod_name, prod_price
FROM Products
WHERE (vend_id = 1001 OR vend_id = 1005)
AND prod_price >= 10;
```

输出

```sql
prod_name prod_price
----------------------
2 ton anvil	14.99
JetPack 1000	35.00
JetPack 2000	55.00
```

## 5.2 IN 操作符

用来指定条件范围，范围中的每个条件都可以进行匹配。用逗号分隔、括在圆括号中的合法值。

输入

```sql
SELECT prod_name, prod_price
FROM Products
WHERE vend_id IN (1001, 1005)
ORDER BY prod_name;
```

输出

```sql
prod_name prod_price
---------------------
.5 ton anvil	5.99
1 ton anvil	9.99
2 ton anvil	14.99
JetPack 1000	35.00
JetPack 2000	55.00
```

这里面的实现与 5.1.2 `OR` 操作符完成了相同的功能。

优点

- 在有很多合法选项时，`IN` 操作符的语法更清楚，更直观
- 在与其他 `AND` 和 `OR` 操作符组合使用 `IN` 时，求值顺序更容易管理
- `IN` 操作符一般比一组 `OR` 操作符执行得更快
- `IN` 的最大优点是可以包含其他 `SELECT` 语句，能否更动态地建立 `WHERE` 子句

## 5.3 NOT 操作符

`WHERE` 子句中的 `NOT` 操作符有且只有一个功能，那就是否定其后跟随的任何条件。记得 `NOT` 关键字是放在过滤的列前。

输入

```sql
SELECT prod_name, prod_price
FROM Products
WHERE NOT vend_id = 1001
ORDER BY prod_name;
```

输出

```sql
prod_name prod_price
TNT (5 sticks)	10.00
TNT (1 stick)	2.50
Sling	4.49
Safe	50.00
Oil can	8.99
JetPack 2000	55.00
JetPack 1000	35.00
Fuses	3.42
Detonator	13.00
Carrots	2.50
Bird seed	10.00
```

这个例子中用 `<>` 操作符来完成也是可以的

```sql
SELECT prod_name, prod_price
FROM Products
WHERE vend_id <> 1001
ORDER BY prod_name;
```

为什么要使用 `NOT`？对于这里的这种简单的 WHERE 子句，使用 `NOT` 关键字确实没有什么优势。但在更复杂的子句中，`NOT` 是非常有用的。例如，与 `IN` 操作符联合使用时，`NOT` 可以非常简单地找出与条件列表不匹配的行。

## 5.4 小结

讲授如何使用 `AND` 和 `OR` 操作符组合成 `WHERE` 子句，讲授了如何明确管理求值顺序，如何使用 `IN` 和 `NOT` 操作符。
