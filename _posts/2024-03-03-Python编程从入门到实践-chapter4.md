---
layout:     post
title:      "Python 编程从入门到实践-chapter4"
date:       2024-03-03
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Python
---

## 操作列表

### 遍历整个元素

```python
magicians = ['alice', 'david', 'canlina']
for magician in magicians:
    print(magician)
```

打印
alice
david
canlina

#### 在 for 循环中执行更多的操作

```python
magicians = ['alice', 'david', 'canlina']
for magician in magicians:
    print(f"{magician.title()}, that was a great trick!")
```

打印
alice, that was a great trick!

david, that was a great trick!

canlina, that was a great trick!

#### 在 for 循环结束后中执行一些操作

```python
magicians = ['alice', 'david', 'canlina']
for magician in magicians:
    print(f"{magician.title()}, that was a great trick!")
print('Thank you, everyone. That was a great magic show!')
```

打印
alice, that was a great trick!

david, that was a great trick!

canlina, that was a great trick!

Thank you, everyone. That was a great magic show!

### 避免缩进错误

#### 忘记缩进

```python
magicians = ['alice', 'david', 'canlina']
for magician in magicians:
print(f"{magician.title()}, that was a great trick!")
```

python 这里将会报错，提示你需要缩进

#### 忘记缩进额外的代码行

```python
magicians = ['alice', 'david', 'canlina']
for magician in magicians:
    print(f"{magician.title()}, that was a great trick!")

print(f"I can't wait to see your next trick, {magician.title()}")
```

打印

alice, that was a great trick!

david, that was a great trick!

canlina, that was a great trick!

I can't wait to see your next trick, Canlina

#### 不必要的缩进

```python
message = 'Hello World'
    print(f"{message}")
```

python 会报错，提示这里无需缩进

#### 遗漏冒号

```python
magicians = ['alice', 'david', 'canlina']
for magician in magicians
    print(f"{magician.title()}, that was a great trick!")
```

python 会报错，提示缺少冒号

### 创建数值列表

#### 使用 range() 函数

```python
for value in range(1, 5):
    print(value)
```

打印
1
2
3
4

#### 使用 range() 创建数值列表

```python
list(range(1, 5))
```

打印 [1, 2, 3, 4]

```python
list(range(1, 5, 2))
```

打印 [1, 3]

#### 对数值列表执行简单的统计计算

min() 计算列表中的最小值

max() 计算列表中的最大值

sum() 计算列表中的总数

#### 列表推导式

```python
squares = [value**2 for value in range(1, 11)]
print(squares)
```

打印 [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

### 使用列表中的一部分

#### 切片

指定使用的第一个元素和最后一个元素的索引（包头不包尾，可省略参数）

```python
plaryers = ['charles', 'martina', 'michael', 'florence', 'eli']
print(plaryers[0:3])
```

打印 ['charles', 'martina', 'michael']

#### 遍历切片

```python
plaryers = ['charles', 'martina', 'michael', 'florence', 'eli']
print(plaryers[0:3])
for player in plaryers[0:3]:
    print(player.title())
```

打印
Charles
Martina
Michael

#### 复制列表

创建的是一个列表的副本，不是同一个引用

```python
plaryers = ['charles', 'martina', 'michael', 'florence', 'eli']
print(plaryers)
copied_players = plaryers[:]
print(copied_players)
```

打印

['charles', 'martina', 'michael', 'florence', 'eli']
['charles', 'martina', 'michael', 'florence', 'eli']

### 元组

使用圆括号定义元组（不能修改元组的元素）

```python
dimensions = (200, 50)
print(dimensions[0])
print(dimensions[1])
```

打印
200
50

#### 遍历元组中的所有值

```python
dimensions = (200, 50)
for dimension in dimensions:
    print(dimension)
```

打印
200
50

#### 修改元组变量

```python
dimensions = (200, 50)
for dimension in dimensions:
    print(dimension)

dimensions = (201, 51)
for dimension in dimensions:
    print(dimension)
```

上述操作是合法的
