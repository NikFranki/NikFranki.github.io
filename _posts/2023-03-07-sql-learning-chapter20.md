---
layout:     post
title:      "SQL learning - chapter20"
date:       2023-03-07
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第20课 事务管理处理

这一课介绍什么是事务处理，如何使用 `COMMIT` 和 `ROLLBACK` 语句管理事务处理。

## 20.1 事务处理

`transaction processing`，确保成批的 `SQL` 操作要么完全执行，要么完全不执行，来维护数据库的完整性。

关键词

- 事务（transaction）指一组 SQL 语句
- 回退（rollback）指撤销指定 SQL 语句的过程
- 提交（commit）指未存储的 SQL 语句结果写入数据库表
- 保留点（savepoint）指事务处理中设置的临时占位符（placeholder），可以对它发布回退

## 20.2 控制事务处理

输入

```sql
BEGIN TRANSACTION
...
COMMIT TRANSACTION
```

### 20.2.1 使用 ROLLBACK

执行回退 SQL 语句。

输入

```sql
DELETE FROM Orders;
ROLLBACK;
```

### 20.2.2 使用 COMMIT

一般的 `SQL` 语句都是针对数据库直接执行和编写的。这就是所谓的隐式提交（implicit commit），即是提交操作是自动进行的。

而事务的处理中，提交不会隐式进行。

输入

```sql
BEGIN TRANSACTION
DELETE OrderItems WHERE order_num = 20009
DELETE Orders WHERE order_num = 20009
COMMIT TRANSACTION
```

### 20.2.3 使用保留点

支持部分提交或回退

输入

```sql
SAVEPOINT delete1;
```

输入

```sql
ROLLBACK TO delete1;
```

## 小结

介绍了事务是必须完整执行的 `SQL` 语句块。学习了如何使用 `COMMIT` 和 `ROLLBACK` 语句对何时写数据、何时回退数据，进行明确管理。学会设定保留点，进行部分的撤销和提交。
