---
layout:     post
title:      "SQL learning - chapter8"
date:       2023-02-23
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第8课 使用函数处理数据

这一课介绍什么是函数，DBMS 支持何种函数，以及如何使用这些函数；还会讲解为什么 `SQL` 函数的使用可能会带来问题。

## 8.1 函数

`SQL` 函数不是可移植的。这意味着为特定 `SQL` 实现编写的代码在其他实现中可能不正常。

## 8.2 使用函数

- 用于处理文本的字符串（删除或填充值、转换值为大写或小写）的文本函数
- 用于在数值数据上进行算术操作（如返回绝对值，进行代数运算）的数值函数
- 用于处理日期和时间值并从这些值中提取特定成分（如返回两个日期之差，检查日期的有效性）的时间函数
- 返回 `DBMS` 正使用的特殊信息（返回用户的登录信息）的系统函数

### 8.2.1 文本函数

输入

```sql
SELECT vend_name, UPPER(vend_name) AS upper_vend_name
FROM Vendors
ORDER BY vend_name;
```

输出

```sql
vend_name upper_vend_name
---------------------------
ACME	ACME
Anvils R Us	ANVILS R US
Furball Inc.	FURBALL INC.
Jet Set	JET SET
Jouets Et Ours	JOUETS ET OURS
LT Supplies	LT SUPPLIES
```

可以看到，`UPPER` 函数将文本数据转换为大写。

下表是常用的文本处理函数

|函数|说明|
|---|---|
|LEFT()|返回字符串左边的字符|
|LENGTH()|返回字符串的长度|
|LOWER()|将字符串转为小写|
|LTRIM()|去掉字符串左边的空格|
|RTRIM()|返回字符串右边的空格|
|RIGHT()|返回字符串右边的字符|
|SOUNDEX()|返回字符串的 SOUNDEX 值|
|UPPER()|将字符串转为大写|

其中，SOUNDEX 是一个将任何文本字符串转换为描述其语音表示的字母数字模式的算法。

输入

```sql
SELECT cust_name, cust_contact
FROM Customers
WHERE SOUNDEX(cust_contact) = SOUNDEX('Jerry Moese');
```

输出

```sql
cust_name cust_contact
-------------------------
Mouse House	Jerry Mouse
```

### 8.2.2 日期和时间处理函数

日期和时间采用相应的数据类型存储在表中，每种 DBMS 都有自己的特殊形式。但是它们很不一致，所以移植性很差。

输入

```sql
SELECT order_num
FROM Orders
WHERE YEAR(order_date) = 2012;
```

输出

```sql
order_num
----------
20005
20006
20007
20008
20009
```

### 8.2.3 数值处理函数

常用数值处理函数

|函数|说明|
|---|---|
|ABS()|返回一个数的绝对值|
|COS()|返回一个角度的余弦|
|EXP()|返回一个数的指数值|
|PI()|返回圆周率|
|SIN()|返回一个角度的正弦|
|SQRT()|返回一个数的平方根|
|TAN()|返回一个角度的正切|

## 8.3 小结

介绍了如何使用 `SQL` 的数据处理函数。虽然这些函数在格式化处理和过滤数据中非常有用，但它们在各种 `SQL` 实现中很不一致。
