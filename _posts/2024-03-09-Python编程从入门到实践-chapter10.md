---
layout:     post
title:      "Python 编程从入门到实践-chapter10"
date:       2024-03-09
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Python
---

## 文件和异常

### 读取文件

#### 读取文件全部内容

```python
from pathlib import Path

path = Path('pi_digits.txt')
contents = path.read_text()
print(contents)
```

#### 相对文件路径和绝对文件路径

```python
from pathlib import Path

path = Path('text_files/filename.txt') # 相对路径
path = Path('/home/eric/data_files/text_files/filename.txt') # 绝对路径
```

#### 读取文件中的各行

```python
from pathlib import Path

path = Path('pi_digits.txt')
contents = path.read_text()

lines = contents.splitlines()
for line in lines:
    print(line)
```

### 写入文件

#### 写入一行

```python
from pathlib import Path

path = Path('pi_digits.txt')
path.write_text('3.14159')
```

#### 写入多行

```python
from pathlib import Path

path = Path('pi_digits.txt')
contents = '3.14'
contents += '159'
path.write_text(contents)
```

### 异常

#### 处理 ZeroDivisionError 异常

```python
print(5/0)
```

Python 会提示 ZeroDivisionError: division by zero

#### 使用 try-except

```python
try:
    print(5/0)
except:
    print("You can't divide by zero!")
```

输出 You can't divide by zero!

#### 使用异常避免崩溃

```python
print("Give me two number, and I'll divide them.")
print("Enter 'q' to quit.")
while True:
    first_number = input("Input the first number")
    if first_number == 'q':
        break
    second_number = input("Input the second number")
    if second_number == 'q':
        break
    try:
        answer = first_number / second_number
    except:
        print("You can't divide by zero!")
    else:
        print(answer)
```

#### 静默失败

```python
print("Give me two number, and I'll divide them.")
print("Enter 'q' to quit.")
while True:
    first_number = input("Input the first number")
    if first_number == 'q':
        break
    second_number = input("Input the second number")
    if second_number == 'q':
        break
    try:
        answer = first_number / second_number
    except:
        pass
    else:
        print(answer)
```

### 存储数据

#### 使用 json.dumps() 和 json.loads()

写入

```python
from pathlib import Path
import json

numbers = [1, 2, 3]
path = Path('number.json')
contents = json.dumps(numbers)
path.write_text(contents)
```

读取

```python
from pathlib import Path
import json

path = Path('number.json')
contents = path.read_text()
numbers = json.loads(contents)
print(numbers)
```
