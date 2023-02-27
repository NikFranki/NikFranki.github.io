---
layout:     post
title:      "SQL learning - chapter15"
date:       2023-03-02
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第15课 插入数据

这一课介绍如何使用 `SQL` 的 `INSERT` 语句将数据插入表中

## 15.1 数据插入

- 插入完整的行
- 插入行的一部分
- 插入某些查询的结果

### 15.1.1 插入完整的行

输入

```sql
INSERT INTO Customers
VALUES (
  NULl,
  'Toy',
  '12 any Street',
  'New York',
  'NY',
  '11',
  'USA',
  NULL,
  NULL
);
```

插入一名新顾客

另外一种写法

输入

```sql
INSERT INTO Customers (
  cust_id,
  cust_name,
  cust_address,
  cust_city,
  cust_state,
  cust_zip,
  cust_country,
  cust_contact,
  cust_email
)
VALUES
(
  NULl,
  'Toy',
  '12 any Street',
  'New York',
  'NY',
  '11',
  'USA',
  NULL,
  NULL
);
```

提供列名，`VALUES` 必须以其指定的次序匹配指定的列名，优点是，即使表的结构发生改变，这条语句仍然能够继续工作。

### 15.1.2 插入部分行

输入

```sql
INSERT INTO Customers (
  cust_id,
  cust_name,
  cust_address,
  cust_city,
  cust_state,
  cust_zip,
  cust_email
)
VALUES
(
  NULl,
  'Toy',
  '12 any Street',
  'New York',
  'NY',
  '11',
  NULL
);
```

### 15.1.3 插入检索出的数据

将 `SELECT` 查询出来的结果，插入的表中。

输入

```sql
INSERT INTO Customers (
  cust_id,
  cust_name,
  cust_address,
  cust_city,
  cust_state,
  cust_zip,
  cust_email
)
SELECT cust_id,
  cust_name,
  cust_address,
  cust_city,
  cust_state,
  cust_zip,
  cust_email
FROM CustNew;
```

## 15.2 从一个表复制到另一个表

将数据复制到一个新表中

```sql
CREATE TABLE CustCopy AS
SELECT * FROM Customers;
```

## 15.3 小结

介绍了如何将行插入到数据库表中。学习了 INSERT 的几种方法，为什么要明确使用列名，如何用其他表导入到新表。
