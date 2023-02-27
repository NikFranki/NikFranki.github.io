---
layout:     post
title:      "SQL learning - chapter22"
date:       2023-03-09
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第22课 高级 SQL 特性

这一课介绍 `SQL` 所涉及的几个高级数据处理特性；约束、索引和触发器。

## 22.1 约束

利用键来建立一个表到另一个表的引用。

### 22.1.1 主键

- 任意两行的主键值都不相同
- 每行都具有一个主键值
- 包含主键值的列从不修改或更新
- 主键值不能重用

关键字 `PRIMARY KEY`

### 22.1.2 外键

输入

```sql
CREATE TABLE Orders
(
  order_num INTEGER NOT NULL PRIMARY KEY,
  order_date DATETIME NOT NULL,
  cust_id CHAR(10) NOT NULL REFERENCES
  Customers(cust_id)
);
```

关键字 `REFERENCES`

输入

```sql
ALTER TABLE Orders
ADD CONSTRAINT
FOREIGN KEY (cust_id) REFERENCES Customers (cust_id);
```

### 22.1.3 唯一约束

- 表可包含多个唯一约束，但是每个表只允许一个主键
- 唯一约束列可包含 NULL 值
- 唯一约束列可修改更新
- 唯一约束列可重复利用
- 与主键不一样，唯一约束不能用来定义外键

### 22.1.4 检查约束

- 检查最大或最小值
- 指定范围
- 只允许特定的值

```sql
CREATE TABLE OrderItems
(
  order_num INTEGER NOT NULL,
  quantity INTEGER NOT NULL CHECK (quantity > 0)
);
```

保证插入或更新的 `quantity` 必须大于0。

## 22.2 索引

用来排序数据以加快搜索和排序操作的速度。

- 索引改善检索操作的性能
- 索引数据可能要占用大量存储空间
- 并非所有的数据都适合索引
- 索引用于数据过滤或数据排序
- 可以在索引定义多个列

输入

```sql
CREATE INDEX prod_name_ind
ON Products (prod_name);
```

索引必须唯一命名。

## 22.3 触发器

触发器是特殊的存储过程，它在特定的数据库活动发生时自动执行。

与单个表关联。

触发器内的代码具有以下数据的访问权

- INSERT 操作中的所有新数据
- UPDATE 操作中的所有新数据和旧数据
- DELETE 操作中删除的数据

```sql
CREATE TRIGGER customer_state
ON Customers
FOR INSERT, UPDATE
AS
UPDATE Customers
SET cust_state = Upper(cust_state)
WHERE Customers.cust_id = inserted.cust_id;
```

## 22.4 数据库安全

用户授权和身份确认

- 对数据库管理功能的访问
- 对特定数据库或表的访问
- 访问的类型（只读、对特定列的访问）
- 仅通过视图或存储过程对表进行访问
- 创建多层次的安全措施，从而允许多种基于登录访问和控制
- 限制管理用户账户的能力

## 22.5 小结

讲授如何使用 `SQL` 的一些高级特性。约束是实施引用完整性的重要部分，索引可改善数据检索的性能，触发器可以用来执行运行前后的处理，安全选项可以用来管理数据的访问。
