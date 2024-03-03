---
layout:     post
title:      "Python 编程从入门到实践-chapter6"
date:       2024-03-05
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Python
---

## 字典

### 一个简单的字典

```python
alien = {'color': 'green', 'points': 6}

print(alien['color'])
print(alien['points'])
```

输出
green
6

### 使用字典

#### 访问字典中的值

```python
alien = {'color': 'green', 'points': 6}

new_points = alien['points']
print(new_points)
```

输出
6

#### 添加键值对

```python
alien = {'color': 'green', 'points': 6}

alien['x_position'] = 0
alien['y_position'] = 25
print(alien)
```

输出 {'color': 'green', 'points': 6, 'x_position': 0, 'y_position': 25}

#### 从创建一个空字典开始

```python
alien = {}

alien['x_position'] = 0
alien['y_position'] = 25
print(alien)
```

输出 {'x_position': 0, 'y_position': 25}

#### 修改字典中的值

```python
alien = {'color': 'green', 'points': 6}
alien['color'] = 'yellow'
print(alien)
```

输出 {'color': 'green', 'points': 6}

#### 删除键值对

```python
alien = {'color': 'green', 'points': 6}
del alien['color']
print(alien)
```

输出 {'points': 6}

#### 使用 get() 方法访问值

```python
alien = {'color': 'green', 'points': 6}
print(alien.get('color', 'No color value assigned.'))
```

输出 green

### 遍历字典

```python
alien = {'color': 'green', 'points': 6}

for key, value in alien.items():
    print(f"\nKey: {key}")
    print(f"Value: {value}")
```

输出
Key: color
Value: green

Key: points
Value: 6

#### 遍历字典中的所有键

```python
alien = {'color': 'green', 'points': 6}

for key in alien.keys():
    print(f"\nKey: {key}")
```

输出
Key: color

Key: points

#### 遍历字典中的所有值

```python
alien = {'color': 'green', 'points': 6}

for value in alien.values():
    print(f"\nValue: {value}")
```

输出
Value: green

Value: 6

### 嵌套

#### 字典列表

```python
alien0 = {'color': 'green', 'points': 6}
alien1 = {'color': 'green', 'points': 6}

aliens = [alien0, alien1]

for alien in aliens:
    print(alien)
```

输出
{'color': 'green', 'points': 6}
{'color': 'green', 'points': 6}

#### 字典中存储列表

```python
alien = {'color': 'green', 'points': 6，decorations: [1, 2, 3]}
```

#### 字典中嵌套字典

```python
alien = {'color': 'green', 'points': 6，decorations: {'weapen': 'gun', 'bult': 'big'}}
```
