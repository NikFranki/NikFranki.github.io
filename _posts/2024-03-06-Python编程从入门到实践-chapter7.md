---
layout:     post
title:      "Python 编程从入门到实践-chapter7"
date:       2024-03-06
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Python
---

## 用户输入和 while 循环

### input() 函数的工作原理

input() 函数让程序暂停运行，等待用户输入一些文本。获取文本输入后，python 将其赋予一个变量，以便使用

```python
message = input("Tell me something, and I will repeat it back to you: ")
print(message)
```

Tell me something, and I will repeat it back to you: Hello
Hello

### while 循环简介

for 循环针对集合中的每个元素执行一个代码块，而 while 循环则不断地运行，直到指定的条件不再满足为止

#### 使用 while 循环

```python
current_number = 1
while current_number <= 5:
    print(current_number)
    current_number += 1
```

输出
1
2
3
4
5

#### 让用户何时退出

```python
message = ''
while message != 'quit':
    message = input("Tell me something, and I will repeat it back to you: ")
    print(message)
```

#### 使用标志

```python
active = True
message = ''
while active:
    message = input("Tell me something, and I will repeat it back to you: ")
    if message == 'quit':
        active = False
    else:
        print(message)
```

#### 使用 break

```python
message = ''
while True:
    message = input("Tell me something, and I will repeat it back to you: ")
    if message == 'quit':
        break
    else:
        print(message)
```

#### 在循环中使用 continue

```python
current_number = 0
while current_number < 10:
    current_number += 1
    if current_number % 2 == 0:
        continue
    print(current_number)
```

输出
1
3
5
7
9

#### 使用 while 循环处理列表和字典

```python
unconfirmed_users = ['alice', 'brian', 'candace']
confirmed_uses = []

while unconfirmed_users:
    current_user = unconfirmed_users.pop()
    print(f"Verifying user: {current_user.title()}")
    confirmed_uses.append(current_user)
print(confirmed_uses)
```

输出
['alice', 'brian', 'candace']
