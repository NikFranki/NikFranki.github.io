---
layout:     post
title:      "SQL learning - chapter10"
date:       2023-02-25
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第10课 分组数据

这一课介绍如何分组数据，以便汇总表内容的子集。主要会有两个语句：`GROUP BY` 子句和 `HAVING` 子句。

## 10.1 数据分组

使用分组可以将数据分为多个逻辑组，对每个组进行聚集计算。

## 10.2 创建分组

输入

```sql
SELECT vend_id, COUNT(*) AS num_prods
FROM Procucts
ORDER BY vend_id;
```

输出

```sql
vend_id num_prods
------------------
1001	3
1002	2
1003	7
1005	2
```

按照 vend_id 指示排序并分组数据

注意事项：

- `GROUP BY` 子句可以包含任意数目的列，因而可以对分组进行嵌套
- 如果在 `GROUP BY` 子句中嵌套了分组，数据将在最后指定的分组上进行汇总
- `GROUP BY` 子句中列出的每一列都必须是检索列或有效的表达式
- 不允许列带有长度可变的数据类型
- 除聚集语句外，SELECT 语句中的每一列都必须在 `GROUP BY` 子句中给出
- 分组中含有 NULL 值的行，则 NULL 将作为一个分组返回，如果列中有多行 NULL 值，它们将分为一组
- `GROUP BY` 子句必须出现在 WHERE 子句之后，ORDER BY 子句之前

## 10.3 过滤分组

过滤分组使用 `HAVING`

输入

```sql
SELECT cust_id, COUNT(*) AS orders
FROM Orders
GROUP BY cust_id
HAVING COUNT(*) > 1;
```

输出

```sql
cust_id orders
----------------
10001	2
```

还可以加入 WHERE 子句来增加限制

输入

```sql
SELECT vend_id, COUNT(*) AS prod_nums
FROM Products
WHERE prod_price >= 4
GROUP BY vend_id
HAVING COUNT(*) > 1;
```

输出

```sql
vend_id prod_nums
-------------
1001	3
1003	5
1005	2
```

## 10.4 分组和排序

`ORDER BY` 与 `GROUP BY` 差异

|ORDER BY|GROUP BY|
|---|---|
|对产生的输出进行排序|对行分组，但输出可能不是分组的顺序|
|任意列都可以使用|只能使用选择列，而且必须使用每个选择列表达式|
|不需要 |如果与聚集函数使用列，则必须使用|

想要排序正确，请务必加上 ORDER BY 子句

输入

```sql
SELECT order_id, COUNT(*) order_items
FROM OrderItems
GROUP BY order_id
HAVING COUNT(*) >= 2
ORDER BY order_items, order_id;
```

输出

```sql
order_num order_items
-----------------
20005	4
20009	4
```

## 10.5 SELECT 子句顺序

`SELECT` 子句及其顺序

|子句|说明|是否必须使用|
|---|---|---|
|SELECT|要返回的列或表达式|是|
|FROM|从中检索的表|仅在从表中选择数据中使用|
|WHERE|行级过滤|否|
|GROUP BY|分组说明|仅在按组计算聚集时使用|
|HAVING|分组过滤|否|
|ORDER BY|输出排序顺序|否|

## 10.6 小结

讲授了如何使用 `GROUP BY` 子句对于多组数据进行汇总计算，返回每个组的结果。如何使用 `HAVING` 对于分组进行过滤，还知道了 `ORDER BY` 和 `GROUP BY` 之间以及 `WHERE` 和 `HAVING` 之间的差异。
