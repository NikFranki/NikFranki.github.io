---
layout:     post
title:      "SQL learning - chapter16"
date:       2023-03-03
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第16课 更新和删除数据

## 16.1 更新数据

- 更新表中的特定行
- 更新表中的所有行

注意：不要忘记 `WHERE` 子句

输入

```sql
UPDATE Customers
SET cust_email = 'xx@ew.com'
WHERE cust_id = 10001;
```

更新多个列

输入

```sql
UPDATE Customers
SET cust_contact = 'www',
  cust_email = 'ui@we.com'
WHERE cust_id = 10006;
```

## 16.2 删除数据

- 从表中删除特定的行

不要忘记 `WHERE` 子句

输入

```sql
DELETE FROM Customers
WHERE cust_id = 10008;
```

## 16.3 更新和删除的指导原则

带上 `WHERE` 子句，是为了更加安全，否则就是应用于所有的行。

## 16.4 小结

讲述如何使用 `UPDATE` 和 `DELETE` 语句处理表中的数据。
