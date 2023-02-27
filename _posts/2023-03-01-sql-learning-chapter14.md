---
layout:     post
title:      "SQL learning - chapter14"
date:       2023-03-01
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第14课 组合查询

本课讲述如何利用 `UNION` 操作符将多条 `SELECT` 语句组合成一个结果集

## 14.1 组合查询

多数 `SQL` 查询只包含从一个或者多个表中返回数据的单条 `SELECT` 语句。但是 `SQL` 也允许多个查询，并将将结果作为一个查询结果集返回。

通常有两种情况需要用到组合查询

- 在一个查询中从不同的表返回结构的数据
- 对一个表执行多次查询，按一个查询返回数据

## 14.1 创建组合查询

### 14.2.1 使用 UNION

输入

```sql
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_state IN ('IL', 'IN', 'MI')
UNION
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_name = 'Wascals';
```

输出

```sql
cust_name cust_contact cust_email
------------------------------------
Coyote Inc.	Y Lee	ylee@coyote.com
Wascals	Jim Jones	rabbit@wascally.com
E Fudd	E Fudd	NULL
```

`SQL` 没有 对 `UNION` 进行限制次数的使用

### 14.2.2 UNION 规则

- `UNION` 必须由两条或者两条以上的 `SELECT` 语句组成
- `UNION` 中的每个查询必须包含相同的列、表达式或聚集函数
- 列数据类型必须兼容

### 14.2.3 包含或取消重复的行

UNION 从查询结果集中自动取出了重复的行；要想改变默认行为，返回所有的行，需要加上 ALL

```sql
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_state IN ('IL', 'IN', 'MI')
UNION ALL
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_name = 'Wascals';
```

### 14.2.4 对组合查询结果进行排序

只能使用 `ORDER BY` 进行排序，必须必须在最后一条 `SELECT` 语句之后加上

输入

```sql
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_state IN ('IL', 'IN', 'MI')
UNION ALL
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_name = 'Wascals'
ORDER BY cust_name, cust_contact;
```

输出

```sql
cust_name cust_contact cust_email
--------------------------------------
Coyote Inc.	Y Lee	ylee@coyote.com
E Fudd	E Fudd	NULL
Wascals	Jim Jones	rabbit@wascally.com
Wascals	Jim Jones	rabbit@wascally.com
```

## 14.1 小结

讲授了如何用 `UNION` 操作符来组合 `SELECT` 语句。利用 `UNION`，可以把多条查询的结果作为一条组合查询返回，不管结果有无重复。
