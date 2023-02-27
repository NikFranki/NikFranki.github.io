---
layout:     post
title:      "SQL learning - chapter19"
date:       2023-03-06
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第19课 使用存储过程

这一课介绍什么是存储过程，为什么要用存储过程，如何使用存储过程，以及如何创建和使用存储过程的基本语法。

## 19.1 存储过程

之前我们使用的大多数 `SQL` 语句都是针对一个或者多个表的单条语句查询。并非所有操作都那么简单，经常会有一些复杂的操作需要多条语句才能完成。

存储过程就是为以后使用保存的一条或多条 `SQL` 语句。可以理解为批文件，虽然它们的作用不仅限于批处理。

## 19.2 为什么要使用存储过程

- 把处理封装在一个易用的单元中，可以简化复杂的操作
- 不要求反复建立一系列处理步骤，因而保证了数据的一致性
- 简化对变动的处理
- 以编译过的形式存储，提高性能
- 用在单个请求中的 SQL 元素和特性，存储过程可以使用它们来编写与功能更强更灵活的代码

## 19.3 执行存储过程

存储过程的执行远比编写要频繁得多，因此我们先介绍存储过程的执行。

输入

```sql
CALL AddNewProduct(
  'JT01',
  'XX',
  6,
  'OOPP'
);
```

AddNewProduct 是存储过程的名称。

## 19.4 创建存储过程

输入

```sql
DELIMITER //
CREATE PROCEDURE MailingListCount (IN cust_id INTEGER)
BEGIN
  SLECT cust_id, cust_name, cust_contact
  FROM Customers
  WHERE cust_id = cust_id; //
DELIMITER;
```

输入

```sql
CALL MailingListCount(10001);
```

输出

```sql
cust_id cust_name cust_contact
----------------------------------
10001	Coyote Inc.	Y Lee
10001	Mouse House	Jerry Mouse
10001	Wascals	Jim Jones
10001	Yosemite Place	Y Sam
10001	E Fudd	E Fudd
10001	Toy	www
10001	Toy	NULL
```

## 小结

介绍了什么是存储过程，为什么要用存储过程。介绍了创建和执行存储过程。
