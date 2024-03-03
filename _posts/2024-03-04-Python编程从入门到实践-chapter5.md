---
layout:     post
title:      "Python 编程从入门到实践-chapter5"
date:       2024-03-04
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Python
---

## if 语句

### 一个简单的示例

```python
cars = ['audi', 'bmw', 'subaru', 'toyota']

for car in cars:
    if car == 'bmw':
        print(car.upper())
    else:
        print(car.title())
```

输出
Audi
BMW
Subaru
Toyota

### 条件测试

#### 检查是否相等

```python
car == 'bmw'
```

输出 True

#### 比较相等时忽略大小写

```python
car.lower() == 'audi'
```

输出 True

#### 检查是否不等

```python
requested_topping = 'mushrooms'
if requested_topping != 'anchovies':
    print('Hold the anchovies!')
```

输出 Hold the anchovies!

#### 数值比较

```python
age == 8

age < 8

age > 8

age != 8
```

#### 检查多个条件

```python
age > 8 and age < 20
age > 8 or age < 20
```

#### 检查特定的值是否在/不在列表中

```python
requested_toppings = ['mushrooms', 'onions', 'pineapple']
'mushrooms' in requested_toppings

'mushrooms' not in requested_toppings
```

输出 True

#### 布尔表达式

```python
game_active = True
can_edit = False
```

### if 语句

#### 简单的 if 语句

```python
if conditional_test:
    do_something
```

#### if-else 语句

```python
if conditional_test:
    do_something
else:
    do_other_thing
```

#### if-elif-else 语句

```python
if conditional_test:
    do_something
elif another_canditional_test:
    do_another_thing
elif another_canditional_test:
    do_another_thing
elif another_canditional_test:
    do_another_thing
elif another_canditional_test:
    do_another_thing
else:
    do_other_thing
```

#### 多个条件

```python
if conditional_test:
    do_something

if another_canditional_test:
    do_another_thing

if another_canditional_test:
    do_another_thing
```

### 使用 if 语句处理列表

#### 检查特殊元素

```python
requested_toppings = ['mushrooms', 'onions', 'pineapple']

for requested_topping in requested_toppings:
    if requested_topping == 'mushrooms':
        print('Sorry, we are out of mushrooms right now.')
    else:
        print(f'Adding {requested_topping}.')
```

输出
Sorry, we are out of mushrooms right now.
Adding onions.
Adding pineapple.

#### 确定非空

```python
requested_toppings = ['mushrooms', 'onions', 'pineapple']

if requested_toppings:
    for requested_topping in requested_toppings:
        if requested_topping == 'mushrooms':
            print('Sorry, we are out of mushrooms right now.')
        else:
            print(f'Adding {requested_topping}.')
```

#### 使用多个列表

```python
available_toppings = ['mushrooms', 'onions', 'pineapple']
requested_toppings = ['mushrooms', 'extra cheese']

for requested_topping in requested_toppings:
    if requested_topping in available_toppings:
        print(f'Adding {requested_topping}.')
    else:
        print(f'Sorry, we are out of {requested_topping} right now.')
```
