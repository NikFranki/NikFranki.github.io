---
layout:     post
title:      "SQL learning - chapter7"
date:       2023-02-22
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第7课 创建计算字段

这一课介绍什么是计算字段，如何创建计算字段，以及如何从应用程序中使用别名引用它们。

## 7.1 计算字段

有时候存储在表中的数据都不是应用程序所需要的，我们需要直接从数据库中检索转换、计算或格式化过的数据，而不是检索出数据，然后再在客户端应用程序中重新格式化。这时候计算字段就派上用场了。

## 7.2 拼接字段

可以把两个列拼接起来，可以用一个特殊的操作符来拼接两个列。

输入

```sql
SELECT Concat(vend_name, '(', vend_country , ')')
FROM Vendors
ORDER BY vend_name;
```

输出

```sql
vend_name, '(', vend_country , ')'
-----------------------------------
ACME(USA)
Anvils R Us(USA)
Furball Inc.(USA)
Jet Set(England)
Jouets Et Ours(France)
LT Supplies(USA)
```

- 存储在 vend_name 列中的名字
- 包含一个空格和一个左圆括号和字符串
- 存储在 vend_country 列中的国家
- 包含一个右圆括号的字符串

去掉右边空格可以使用 `RTRIM()` 函数来完成

输入

```sql
SELECT RPRIM(vend_name) , ' (' , RPRIM(vend_country) , ')'
FROM Vendors
ORDER BY vend_name;
```

输出

```sql
RPRIM(vend_name) , ' (' , RPRIM(vend_country) , ')'
-----------------------------------------------------
ACME	 (	USA	)
Anvils R Us	 (	USA	)
Furball Inc.	 (	USA	)
Jet Set	 (	England	)
Jouets Et Ours	 (	France	)
LT Supplies	 (	USA	)
```

使用别名

可在数据库查询工具中查到，但是无法在客户端中使用

```sql
SELECT RTRIM(vend_name), '(', RTRIM(vend_country), ')'
AS vend_title
FROM Vendors
ORDER BY vend_name;
```

## 7.3 执行算术运算

对检索出来的数据进行算术运算

输入

```sql
SELECT prod_id,
      quantity,
      item_price,
      quantity * item_price AS expanded_price
FROM OrderItems
WHERE order_num = 20008;
```

输出

```sql
prod_id quantity item_price expanded_price
------------------------------------------
FC	50	2.50	125.00
```

SQL 支持下表中列出的基础算术操作符。此外，圆括号可以区分优先级。

| 操作符 | 说明  |
| ---   |  --- |
|   +   |  加  |
|   -   |  减  |
|   *   |  乘  |
|   /   |  除  |

## 7.4 小结

介绍了计算字段以及如何使用计算字段。举例说明了计算字段在拼接字符串和算术计算中的用途。还讲述了如何创建和使用别名，以便应用程序能引用计算字段。
