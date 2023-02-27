---
layout:     post
title:      "SQL learning - chapter17"
date:       2023-03-04
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第17课 创建和操纵表

## 17.1 创建表

- 多数 `DBMS` 都具有交互性创建和管理数据库的工具
- 表也可以直接用 `SQL` 语句操纵

### 17.1.1 表创建基础

- 新表的名字，在关键字 `CREATE TABLE` 之后给出
- 表列的名字和定义，用逗号分隔
- 有的 `DBMS` 需要指定表的位置

输入

```sql
CREATE TABLE ProductsNew
(
  prod_id CHAR(10) NOT NULL,
  vend_id CHAR(10) NOT NULL,
  prod_name CHAR(254) NOT NULL,
  prod_price DECIMAL(8, 2) NOT NULL,
  prod_desc VARCHAR(1000) NULL
);
```

### 17.1.2 使用 NULL 值

允许 NULL 值的列也允许在插入行时不给出该列的值，如果不设置，默认为 NULL。

### 17.1.3 指定默认值

SQL 允许指定默认值，在插入时如果不给出值，默认采用默认值。

输入

```sql
CREATE TABLE OrderItemsNew
(
  order_num INTEGER NOT NULL,
  order_item INTEGER NOT NULL,
  prod_id CHAR(10) NOT NULL,
  quantity INTEGER NOT NULL DEFAULT 1,
  item_price DECIMAL(8, 2) NOT NULL
);
```

## 17.2 更新表

输入

```sql
ALTER TABLE Vendors
ADD vend_phone CHAR(20);
```

这条语句添加一个名为 vend_phone 的列。

## 17.3 删除表

输入

```sql
DROP TABLE ProductsNew;
```

## 17.4 重命名表

使用 RENAME 语句

## 17.5 小结

介绍了几条新的 `SQL` 语句。`CREATE TABLE` 来创建新表，`ALTER TABLE` 用来更新表列，而 `DROP TABLE` 用来完整地删除一个表。
