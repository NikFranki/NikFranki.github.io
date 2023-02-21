---
layout:     post
title:      "SQL learning - chapter11"
date:       2023-02-26
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第11课 使用子查询

这一课介绍什么是子查询，如何使用它们。

## 11.1 子查询

SQL 允许创建子查询（subquery），即嵌套在其他查询中的查询。

## 11.2 利用子查询进行过滤

输入

```sql
SELECT cust_id
FROM Orders
WHERE order_num IN (
  SELECT order_num
  FROM OrderItems
  WHERE prod_id = 'FB'
);
```

输出

```sql
cust_id
----------
10001
10001
```

在 SELECT 语句中，子查询总是从内向外处理。

还可以继续嵌套，SQL 对于嵌套的数目没有限制。

输入

```sql
SELECT cust_name, cust_contact
FROM Customers
WHERE cust_id IN (
  SELECT cust_id
  FROM Orders
  WHERE order_num IN (
    SELECT order_num
    FROM OrderItems
    WHERE prod_id = 'FB'
  )
);
```

输出

```sql
cust_name cust_contact
---------------------------
Coyote Inc.	Y Lee
```

## 11.3 作为计算字段使用子查询

输入

```sql
SELECT cust_name, cust_state, (
  SELECT COUNT(*)
  FROM Orders
  WHERE Orders.cust_id = Customers.cust_id
) AS orders
FROM Customers
ORDER BY cust_name;
```

输出

```
cust_name cust_state orders
----------------------------
Coyote Inc.	MI	2
E Fudd	IL	1
Mouse House	OH	0
Wascals	IN	1
Yosemite Place	AZ	1
```

orders 字段就是通过子查询得出的结果

## 11.4 小结

学习了什么是子查询，如何使用它们。子查询常用于 WHERE 子句的 IN 操作符中，以及用来填充计算列。
