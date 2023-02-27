---
layout:     post
title:      "SQL learning - chapter13"
date:       2023-02-28
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第13课 创建高级联结

本课讲解另外一些联结，介绍如何使用表别名，如何对呗联结的表使用聚集函数。

## 13.1 使用表别名

好处：

- 缩短 SQL 语句
- 允许在一条 SELECT 语句上多次使用相同的表

输入

```sql
SELECT cust_name, cust_contact
FROM Customers AS C, Orders AS O, OrderItems AS OI
WHERE C.cust_id = O.cust_id
AND O.order_num = OI.order_num
AND OI.prod_id = 'ANV02';
```

输出

```sql
cust_name cust_contact
------------------------
Coyote Inc.	Y Lee
```

## 13.2 使用不同类型的联结

自联结（self-join）、自然联结（natural join）和外联结（outer join）

### 13.2.1 自联结

输入

```sql
SELECT c1.cust_id, c1.cust_name, c1.cust_contact
FROM Customers AS c1, Customers AS c2
WHERE c1.cust_name = c2.cust_name
AND c2.cust_contact = 'Jim Jones';
```

输出

```sql
cust_id cust_name cust_contact
-------------------------------
10003	Wascals	Jim Jones
```

### 13.2.2 自然联结

输入

```sql
SELECT C.*, O.order_num, O.order_date,
  OI.prod_id, OI.quantity, OI.item_price
FROM Customers AS C, Orders AS O, OrderItems AS OI
WHERE C.cust_id = O.cust_id
AND OI.order_num = O.order_num
AND prod_id = 'ANV02';
```

输出

```sql
10001	Coyote Inc.	200 Maple Lane	Detroit	MI	44444	USA	Y Lee	ylee@coyote.com	20005	2005-09-01 00:00:00	ANV02	3	9.99
```

自然联结排除多次出现，使每一列只返回一次。

### 12.2.3 外联结

输入

```sql
SELECT Customers.cust_id, Orders.order_num
FROM Customers LEFT OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id;
```

输出

```sql
cust_id order_num
-------------------
10001	20005
10001	20009
10002	NULL
10003	20006
10004	20007
10005	20008
```

想要右外连接，可以把 LEFT 换位 RIGHT

输入

```sql
SELECT Customers.cust_id, Orders.order_num
FROM Customers RIGHT OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id;
```

想要全连接，可以把 LEFT 换位 FULL

输入

```sql
SELECT Customers.cust_id, Orders.order_num
FROM Customers FULL OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id;
```

左外连接即是连接左边的表；右外连接是连接右边的表；全外连接包含两个表不相关的行；

## 13.3 使用带聚集函数的联结

输入

```sql
SELECT Customers.cust_id, COUNT(Orders.order_num) AS order_num
FROM Customers INNER JOIN Orders
ON Customers.cust_id = Orders.cust_id
GROUP BY Customers.cust_id;
```

输出

```sql
cust_id order_num
-----------------
10001	2
10003	1
10004	1
10005	1
```

## 13.4 使用联结和条件

- 注意联结类型，一般使用内联结
- 具体用法看具体的 DBMS 文档
- 保证使用正确的联结条件
- 一个联结中可以使用多张表

## 13.5 小结

讲授了为什么使用别名，然后谈论了不同联结类型以及使用联结的语法。
