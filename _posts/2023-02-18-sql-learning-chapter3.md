---
layout:     post
title:      "SQL learning - chapter3"
date:       2023-02-18
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# 第3课 排序检索数据

这一节课，主要讲如何使用 `SELECT` 语句的 `ORDER BY`  子句，根据需要排序检索出的数据。

## 3.1 排序数据

排序数据一般按照添加进去的时间来排序，但是进过更新和删除后，DBMS 经过重用和回收存储空间，会对输出的顺序进行变更，所以通过这种方式得到数据的顺序没有什么意义。

可以使用 `ORDER BY` 子句

输入

```sql
SELECT prod_name
FROM Products
ORDER BY prod_name;
```

输出

```sql
prod_name
--------------
.5 ton anvil
1 ton anvil
2 ton anvil
Detonator
Bird seed
Carrots
Fuses
JetPack 1000
JetPack 2000
Oil can
Safe
Sling
TNT (1 stick)
TNT (5 sticks)
```

除了指示 DBMS 软件对于 prod_name 列以字母排序顺序数据的 `ORDER BY` 子句外，跟之前查询语句无异。

注意，`ORDER BY` 子句要放在最后一条子句。如果不是的话，会报错。

## 3.2 按多个列排序

输入

```sql
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY prod_price, prod_name;
```

输出

```sql
prod_id prod_price prod_name
------------------------------
FC	2.50	Carrots
TNT1	2.50	TNT (1 stick)
FU1	3.42	Fuses
SLING	4.49	Sling
ANV01	5.99	.5 ton anvil
OL1	8.99	Oil can
ANV02	9.99	1 ton anvil
FB	10.00	Bird seed
TNT2	10.00	TNT (5 sticks)
DTNTR	13.00	Detonator
ANV03	14.99	2 ton anvil
JP1000	35.00	JetPack 1000
SAFE	50.00	Safe
JP2000	55.00	JetPack 2000
```

仅在多个行具有相同的 prod_price 值时才会 prod_name 进行排序。如果 prod_price 列中所有的值都是唯一的，则不会按照 prod_name 排序。

## 3.3 按列位置排序

输入

```sql
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY 2, 3;
```

输出

结果跟 3.2 的输出一样，不同之处在于 ORDER BY 子句。选择列中饿相对位置。好处是不用重新输入列名，缺点是不明确列名有可能造成错用列名排序。

## 3.4 指定排序方向

降序排序，可以使用 `DESC`，例如把加个最贵的放在前面

输入

```sql
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY prod_price DESC;
```

输出

```sql
prod_id prod_price prod_name
-----------------------------
JP2000	55.00	JetPack 2000
SAFE	50.00	Safe
JP1000	35.00	JetPack 1000
ANV03	14.99	2 ton anvil
DTNTR	13.00	Detonator
FB	10.00	Bird seed
TNT2	10.00	TNT (5 sticks)
ANV02	9.99	1 ton anvil
OL1	8.99	Oil can
ANV01	5.99	.5 ton anvil
SLING	4.49	Sling
FU1	3.42	Fuses
FC	2.50	Carrots
TNT1	2.50	TNT (1 stick)
```

注意，如果对多列进行降序排序，每一列上都得加上 `DESC` 关键字。

## 3.5 小结

学习了如何用 `SELECT` 语句的 `ORDER BY` 子句对于检索出来的数据进行排序，这个子句必须最后一条子句。可以对于一个或者多个列进行排序。
