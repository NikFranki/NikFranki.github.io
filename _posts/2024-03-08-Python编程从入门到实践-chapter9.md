---
layout:     post
title:      "Python 编程从入门到实践-chapter9"
date:       2024-03-08
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Python
---

## 类

### 创建和使用类

#### 创建 Dog 类

```python
class Dog:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def sit(self):
        print(f"{self.name} is now sitting.")

    def roll_over(self):
        print(f"${self.name} rolled over")
```

__init__() 初始化方法，创建实例的时候会调用它

#### 根据类创建实例

```python
class Dog:
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self.feather = 'thick'
    
    def sit(self):
        print(f"{self.name} is now sitting.")

    def roll_over(self):
        print(f"{self.name} rolled over.")

my_dog = Dog('willie', 6)

print(my_dog.name)
print(my_dog.age)
print(my_dog.feather)

my_dog.sit()
my_dog.roll_over()

my_dog.feather = 'thin'
print(my_dog.feather)
```

输出
willie
6
thick
willie is now sitting.
willie rolled over.
thin

### 继承

```python
class Dog:
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self.feather = 'thick'
    
    def sit(self):
        print(f"{self.name} is now sitting.")

    def roll_over(self):
        print(f"{self.name} rolled over.")

class Gift:
    def __init__(self):
        self.gift = 'bone'

class Husky(Dog):
    def __init__(self, name, age):
        super().__init__(name, age)
        self.size = 'big'
        self.gift = Gift()

    def describe_mouth(self):
        print(f"Husky has a {self.size} body.")

husky = Husky('husky', 10)
print(husky.name)
print(husky.age)
print(husky.gift.gift)
husky.describe_mouth()
```

输出
husky
10
bone
Husky has a big body.

### 导入类

#### 导入单个类

```python
from dog import Dog, Gift
from dog import *
from dog import Dog as dg
```
