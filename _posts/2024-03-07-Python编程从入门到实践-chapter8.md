---
layout:     post
title:      "Python 编程从入门到实践-chapter8"
date:       2024-03-07
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Python
---

## 函数

### 定义函数

```python
def greet_user():
    """显示简单的问候语"""
    print("Hello!")

greet_user()
```

输出 Hello!

#### 向函数传递参数

```python
def greet_user(username):
    """显示简单的问候语"""
    print(f"Hello, {username}")

greet_user('franki')
```

输出 Hello, franki

#### 实参和形参

```python
# username 为形参
def greet_user(username):
    """显示简单的问候语"""
    print(f"Hello, {username}")

# franki 为实参
greet_user('franki')
```

输出 Hello, franki

### 传递实参

#### 位置实参

基于实参的顺序进行关联

```python
def greet_user(username):
    """显示简单的问候语"""
    print(f"Hello, {username}")

greet_user('franki')
```

输出 Hello, franki

#### 关键字实参

传递函数的键值对

```python
def greet_user(username):
    """显示简单的问候语"""
    print(f"Hello, {username}")

greet_user(username='franki')
```

输出 Hello, franki

#### 默认值

传递函数的键值对

```python
def greet_user(username='franki'):
    """显示简单的问候语"""
    print(f"Hello, {username}")

greet_user()
```

输出 Hello, franki

#### 等效函数调用

传递函数的键值对

```python
def greet_user(username):
    """显示简单的问候语"""
    print(f"Hello, {username}")

greet_user('franki')
greet_user(username='franki')
```

输出
Hello, franki
Hello, franki

### 返回值

#### 返回简单的值

```python
def get_formatted_name(first_name, last_name):
    full_name = f"{first_name} {last_name}"
    return full_name

musician = get_formatted_name('franki', 'nik')
print(musician)
```

输出 franki nik

#### 让参数变得可选

```python
def get_formatted_name(first_name, last_name, middle_name = ''):
    if middle_name:
        full_name = f"{first_name} {middle_name} {last_name}"
    else:
        full_name = f"{first_name} {last_name}"
    
    return full_name

musician = get_formatted_name('franki', 'nik', 'h')
print(musician)
```

输出 franki h nik

#### 返回字典

```python
def build_person(first_name, last_name):
    
    person = {'first': first_name, 'last': last_name}
    return person

musician = build_person('franki', 'nik')
print(musician)
```

输出 {'first': 'franki', 'last': 'nik'}

#### 结合使用函数和 while 循环

```python
def get_formatted_name(first_name, last_name):
    full_name = f"{first_name} {last_name}"
    return full_name.title()

while True:
    print("\nPlease tell me your name:")
    print("enter 'q' at any time to quit")
    f_name = input("First name")
    if f_name == 'q':
        break
    l_name = input("Last name")
    if l_name == 'q':
        break

    formatted_name = get_formatted_name(f_name, l_name)
    print(formatted_name)
```

#### 传递列表

```python
def greet_users(names):
    for name in names:
        msg = f"Hello, {name.title()}!"
        print(msg)

greet_users(['hannah', 'ty'])
```

输出
Hello, Hannah!
Hello, Ty!

#### 传递任何数量的实参

```python
def make_pizza(*toppings):
    print(toppings)

make_pizza('mashrooms', 'green peppers')
```

输出 ('mashrooms', 'green peppers')

#### 使用任何数量的关键字实参

```python
def build_profile(first, last, **user_info):
    user_info['first_name'] = first
    user_info['last_name'] = last
    return user_info

user_profile = build_profile('albert', 'einstein', location='princeton', field='physics')
print(user_profile)
```

输出 {'location': 'princeton', 'field': 'physics', 'first_name': 'albert', 'last_name': 'einstein'}

### 将函数存储在模块中

pizza.py

```python
def make_pizza(*toppings):
    print(toppings)

make_pizza('mashrooms', 'green peppers')
```

making_pizza.py

```python
from pizza import pizza

pizza.make_pizza()
```

#### 导入特定的函数

```python
from module_name import function_name, function_name1, function_name2
```

#### 使用 as 给函数使用别名

```python
from pizza import make_pizza as mp
```

#### 使用 as 给模块使用别名

```python
from module_name as mn
```

#### 导入模块中的所有函数

```python
from module_name import *
```
