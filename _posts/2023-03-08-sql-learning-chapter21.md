---
layout:     post
title:      "SQL learning - chapter21"
date:       2023-03-08
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第21课 使用游标

这一课讲授什么事游标，如何使用游标。

## 21.1 游标

`SQL` 检索返回的一组成为结果集的行，这组返回的行都是与 `SQL` 语句相匹配的行。简单地使用 `SELECT` 语句，没有办法得到第一行、下一行或者前10行。

- 能够标记游标为只读，使得数据能读取，但不能更新和删除
- 能控制可以执行的定向操作（向前、向后、第一、最后、绝对位置、相对位置等）
- 能标记某些列为可编辑的，某些列为不可编辑
- 规定范围，使游标对创建它的特定请求
- 只是 DBMS 对检索出的数据进行复制，使数据在游标打开和访问期间不变化

## 21.2 使用游标

步骤

- 第一步，必须声明定义它
- 声明后就必须使用它
- 对于有数据的游标，根据需要取出（检索）各行
- 在结束游标的使用时，必须关闭游标

### 21.2.1 创建游标

输入

```sql
DECLARE CustCursor CURSOR
FOR
SELECT * FROM Customers
WHERE cust_email IS NULL;
```

使用 `DECLARE` 关键字创建游标。

### 21.1.2 使用游标

输入

```sql
OPEN CURSOR CustCursor;
```

### 21.1.3 关闭游标

输入

```sql
CLOSE CustCursor;
```

## 小结

讲授什么是游标，为什么使用游标。
