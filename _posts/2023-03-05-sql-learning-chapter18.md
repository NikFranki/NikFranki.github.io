---
layout:     post
title:      "SQL learning - chapter18"
date:       2023-03-05
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - SQL
    - MySQL
---

# 第18课 使用视图

这一课介绍什么是视图，它们怎么工作，何时使用它们。还将讲述如何利用视图简化前几课执行的 `SQL` 操作。

## 18.1 视图

视图是虚拟的表。视图只包含使用时动态检索数据的查询。

### 18.1.1 为什么使用视图

- 重用 `SQL` 语句
- 简化复杂的 `SQL` 操作
- 使用表的一部分
- 保护数据
- 更改数据格式和表示

### 18.1.2 视图的规则和限制

创建视图前，应该要知道一些限制。

- 视图名称要唯一
- 对创建视图的数目没有限制
- 具有足备的访问权限
- 视图可以嵌套
- 不能用索引

## 18.2 创建视图

视图使用 `CARETE VIEW` 语句创建。

### 18.2.1 利用视图简化复杂的联结

输入

```sql
CREATE VIEW ProductCustomers AS 
SELECT cust_name, cust_contact, prod_id
FROM Customers, Orders, OrderItems
WHERE Customers.cust_id = Orders.cust_id
  AND OrderItems.order_num = Orders.order_num;
```

使用视图查询

输入

```sql
SELECT cust_name, cust_contact
FROM ProductCustomers
WHERE prod_id = 'FB';
```

输出

```sql
cust_name cust_contact
-----------------------
Coyote Inc.	Y Lee
Coyote Inc.	Y Lee
```

### 18.2.2 用视图重新格式化检索出的数据

输入

```sql
CREATE VIEW VendorLocations AS
SELECT RTRIM(vend_name) , ' (' , RTRIM(vend_country) , ')'
  AS vend_title
FROM Vendors;
```

```sql
SELECT *
FROM VendorsLocations;
```

输出

```sql
RTRIM(vend_name)  (RTRIM(vend_country))
---------------------------------------
Anvils R Us	 (	USA	)
LT Supplies	 (	USA	)
ACME	 (	USA	)
Furball Inc.	 (	USA	)
Jet Set	 (	England	)
Jouets Et Ours	 (	France	)
```

### 18.2.3 用视图过滤不想要的数据

输入

```sql
CREATE VIEW CustomerEMailList AS
SELECT cust_id, cust_name, cust_email
FROM Customers
WHERE cust_email IS NOT NULL;
```

输入

```sql
SELECT *
FROM CustomerEMailList;
```

输出

```sql
cust_id cust_name cust_email
--------------------------------
10001	Coyote Inc.	xx@ew.com
10003	Wascals	rabbit@wascally.com
10004	Yosemite Place	sam@yosemite.com
10006	Toy	ui@we.com
```

### 18.2.4 使用视图与计算字段

输入

```sql
CREATE VIEW OrderItemsExpanded AS
SELECT order_num,
  prod_id,
  quantity,
  item_price,
  quantity*item_price AS expanded_price
FROM OrderItems;
```

输入

```sql
SELECT * FROM OrderItemsExpanded
WHERE order_num = 20008;
```

输出

```sql
order_num prod_id quantity item_price expanded_price
------------------------------------------------------
20008	FC	50	2.50	125.00
```

## 18.3 小结

视图为虚拟的表。它们包含的不是数据，而是根据需要检索数据的查询。提供一种封装的能力，可以用来简化数据处理，重新格式化或保护基础数据。
