---
layout:     post
title:      "SQL learning - chapter2"
date:       2023-02-17
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第2课 检索数据

这一课介绍如何使用 SELECT 语句从表中检索一个或者多个数据列

## 2.1 SELECT 语句

SQL 由简单的英语单词组成，这些单词称为关键字，每个 SQL 语句都是由一个或多个关键字组成。最常用的就是 SELECT 语句了。用途是从一个或者多个表中检索信息。

## 2.2 检索单个列

简单的 SQL SELECT 语句，如下所示：

输入

```sql
SELECT prod_name
FROM Products;
```

上述语句利用 SELECT 语句从 Products 表中检索出一个表名为 prod_name 的列。所需列名写在 SELECT 关键字后，FROM 关键字指出从哪个表中检索数据。

输出

<img src="images/2.2-select-single.jpg" style="zoom:50%" />

注意：这里面的查询并没有经过排序，不同的 DBMS 查询出来的结果顺序可能不一致

提示：结束 SQL 语句

结束一般会带上分好

提示：SQL 语句和大小写

SQL 语句不区分大小写，规范的写法是关键字大写，表名和列名用小写

提示：使用空格

处理 SQL 语句时，其中所有空格都被忽略。通常将 SQL 语句分为多行更容易阅读和调试

## 2.3 检索多个列

SELECT 关键字后要加上多个列名，列名之间必须使用空格分开。

输入

```sql
SELECT prod_id, prod_name, prod_price
FROM Products;
```

输出

<img src="images/2.3-select-multi.jpg" style="zoom:50%" />

## 2.4 检索所有列

除了指定所需的列外，SELECT 语句还可以检索所有的列而不必逐个列出它们。在实际的列名的位置使用星号 （*）通配符可以做到这点，如下所示：

输入：

```sql
SELECT *
FROM Products;
```

输出

<img src="images/2.4-select-*.jpg" style="zoom:50%" />

返回表中所有的列。列的顺序一般是列在表定义中出现的物理顺序，单并不总是如此。

## 2.4 检索不同的值

只检索 vend_id 列

输入

```sql
SELECT vend_id
FROM Products;
```

结果跟 2.2 类似，只不过列名不一样

如果要去掉重复的供应商，可以使用 `DISTINCT` 关键字

输入

```sql
SELECT DISTINCT vend_id
FROM Products;
```

输出

```sql
vend_id
----------
1001
1002
1003
1005
```

## 2.4 限制结果

返回固定数量的行，可以使用 `LIMIT` 关键字，不同的 DBMS 关键字可能不同，这边使用的是 mysql，所以使用 `LIMIT` 关键字。

输入

```sql
SELECT prod_name
FROM Products
LIMIT 5;
```

输出

```sql
prod_name
-----------------
.5 ton anvil
1 ton anvil
2 ton anvil
Detonator
Bird seed
```

如果想获取后面的5行数据，可以这样，加上 `OFFSET`

输入

```sql
SELECT prod_name
FROM Products
LIMIT 5 OFFSET 5;
```

输出

```sql
prod_name
---------------
Carrots
Fuses
JetPack 1000
JetPack 2000
Oil can
```

这里第一个数字是检索的行数，第二个数字是从哪一行开始。LIMIT 指定返回的行数；OFFSET 指定从哪儿开始。

## 2.4 使用注释

随着 SQL 语句开始边长，复杂性增大后，就会使用添加一些注释

使用于 SQL 文件开始处，加入程序员的联系方式、程序描述以及一些说明

注释的另一个应用就是暂停停滞执行的 SQL 代码

输入

```sql
SELECT prod_name -- 这是一条注释
FROM Products;
```

使用 -- 内嵌在行内，后面的就是注释

输入

```sql
# 这是一条注释
SELECT prod_name
FROM Products;
```

行注释

输入

```sql
/* SELECT prod_name
FROM Products; */
```

段注释

## 2.4 小结

学习了如何使用 SQL 的 SELECT 语句进行检索单个表列、多个表列以及所有表列。也学习了如何返回不同的值，如何注释代码。
