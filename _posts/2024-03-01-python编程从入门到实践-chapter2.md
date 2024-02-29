---
layout:     post
title:      "python编程从入门到实践-chapter2"
date:       2024-03-01
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - Design Patterns
---

## 变量和简单的数据类型

### 变量

创建一个文件名叫做 hello_world.py 的文件，内容为：

```shell
message = "Hello Python World!"
print(message)
```

在终端中使用命令 python3 hello_world.py，将会输出 Hello Python World!

### 变量的命名和使用

变量命名需要遵从以下的规则

- 变量名只能包含字母、数字和下划线。变量名只能以字母或下划线开头，数字不能作为开头
- 变量名不能包含空格

### 字符串

字符串就是一系列字符。在 Python 中，用引号引起的都是字符串，其中航的引号可以单引号，也可以是双引号

```shell
"This is a string."
'This is also a string'
```

#### 字符串大小写

首字母大写

```python
name = "ada lovelace"
print(name.title())
```

将会输出 Ada lovelace

字母全大写、全小写

```python
name = "Ada lovelace"
print(name.upper()) # 全大写
print(name.lower()) # 全小写
```

将会输出
ADA LOVELACE
ada lovelace

#### 字符串中使用变量

```python
first_name = 'ada'
last_name = 'lovelace'
full_name = f"{first_name} {last_name}"
print(full_name)
```

字符f开头的称为f字符串，f为 format 的缩写，意思是格式化字符串，上面的结果输出为

ada lovelace

#### 使用制表符或换行符增加空白

制表符 \t 换行符 \n

```shell
print("Languages: \nPython\nC\nJavaScript")
```

输出结果为

Languages:
Python
C
JavaScript

#### 删除空白

删除左边空白

```python
message = " hello "
print(message.lstrip())
```

输出 `hello `

删除右边空白

```python
message = " hello "
print(message.rstrip())
```

输出 ` hello`

删除全部空白

```python
message = " hello "
print(message.strip())
```

输出 `hello`

#### 删除前缀后缀

删除前缀

```python
message = "hello.txt"
message.removeprefix("hello")
```

输出 .txt

删除后缀

```python
message = "hello.txt"
message.removesuffix(".txt")
```

输出 hello

### 数字

#### 整数

```python
a = 1 + 1 # 加
b = 2 - 1 # 减
c = 2 * 1 # 乘
d = 2 / 1 # 除
```

#### 浮点数

```python
a = 0.1 + 0.1 # 加
b = 0.2 - 0.1 # 减
c = 0.2 * 0.1 # 乘
d = 0.2 / 0.1 # 除
```

需要注意的是，结果包含的小数位是不确定的，例如

0.1 + 0.2 =
0.30000000000000004

#### 整数和浮点数

两个数相除，结果总是浮点数

4 / 2 = 2.0

一个整数一个浮点数相加，结果也是浮点数

1 + 0.2 = 0.3

#### 数字中的下划线

```python
universe_age = 14_000_000_000
print(universe_age)
```

打印的结果是：14000000000

#### 同时给多个变量赋值

```python
a, b, c = 0, 1, 2
```

### 常量

```python
MAX_CONNECTIONS = 500
```

### 注释

```python
# 这是一个注释
```
