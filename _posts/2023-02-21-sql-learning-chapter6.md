---
layout:     post
title:      "SQL learning - chapter6"
date:       2023-02-21
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第6课 用通配符进行过滤

这一课介绍什么是通配符、如何使用通配符以及怎么使用 LIKE 操作符进行通配符搜索，以便于对数据进行复杂过滤。

## 6.1 LIKE 操作符

前面介绍的都是根据已知值进行过滤。但是，这种过滤方法并不是任何时候都是适用的，例如，包含某些特定文本的所有产品是如何查询的呢？答案是必须使用通配符（wildcard）。

为在搜索子句中使用通配符，必须使用 LIKE 操作符。

### 6.1.1 百分号（%）通配符

最常使用的通配符是百分号（%）。在搜索中，%表示任何字符出现任意次数。

下面例子中，找出所有以词 TNT 起头的产品。

输入

```sql
SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE 'TNT%';
```

输出

```sql
prod_id prod_name
TNT1	TNT (1 stick)
TNT2	TNT (5 sticks)
```

通配符可在搜索模式中的任意位置使用，并且可以使用多个通配符。

下面的例子使用两个通配符，它们位于模式的两端。

输入

```sql
SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE '%ton%';
```

输出

```sql
prod_id prod_name
-------------------
ANV01	.5 ton anvil
ANV02	1 ton anvil
ANV03	2 ton anvil
DTNTR	Detonator
```

搜索模式 `%ton%` 表示匹配任何位置上包含文本 `ton` 的值，不论它之前或之后出现什么字符。

通配符也可以出现在中间

输入

```sql
SELECT prod_name
FROM Products
WHERE prod_name LIKE '1%l';
```

输出

```sql
prod_name
------------
1 ton anvil
```

### 6.1.2 下划线（_）通配符

另一个有用的通配符是下滑线（_）。下划线的用途与 % 一样，但它只匹配单个字符，而不是多个字符。

输入

```sql
SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE '__fe';
```

输出

```sql
prod_id prod_name
--------------------
SAFE	Safe
```

WHERE 子句中的搜索模式给出了后面跟有文本的两个通配符。

### 6.1.3 方括号（[]）通配符

方括号（[]）通配符用来指定一个字符集，它必须匹配指定位置的一个字符。

输入

```sql
SELECT cust_contact
FROM Customers
WHERE cust_contact LIKE '[CM]%'
ORDER BY cust_contact;
```

## 6.2 使用通配符的技巧

`SQL` 通配符很有用，但这种功能是有代价的，即通配符搜索一般比前面讨论的其他搜索要耗费更长对的处理时间。

- 不要过度使用通配符。如果其他操作符可以达到目的，应该使用其他操作符
- 在确实需要通配符时，也尽量不要把它们用在搜索模式的开始处
- 仔细注意通配符的位置，如果放错地方，可能返回不想要的数据

## 6.3 小结

介绍什么是通配符，如何在 `WHERE` 子句中使用 `SQL` 通配符，还说明了通配符应该细心使用，不要使用过度。
