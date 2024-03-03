---
layout:     post
title:      "Python 编程从入门到实践-chapter3"
date:       2024-03-02
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Python
---

## 列表

### 列表是什么

列表由一系列特定顺序排序的元素组成。元素之间可以没有任何关系。

示例：

```python
bicycles = ['trek', 'cannondale', 'redline', 'specialized']
print(bicycles)
```

输出 ['trek', 'cannondale', 'redline', 'specialized']

#### 访问元素

```python
bicycles = ['trek', 'cannondale', 'redline', 'specialized']
print(bicycles[0])****
```

输出 trek

### 修改、添加和删除元素

#### 修改列表元素

```python
motorcycles = ['honda', 'yamaha', 'suzuki']
print(motorcycles)

motorcycles[0] = 'ducati'
print(motorcycles)
```

输出

['honda', 'yamaha', 'suzuki']
['ducati', 'yamaha', 'suzuki']

#### 在列表中添加元素

在列表的末尾插入

```python
motorcycles = ['honda', 'yamaha', 'suzuki']
print(motorcycles)

motorcycles.append('ducati')
print(motorcycles)
```

输出

['honda', 'yamaha', 'suzuki']
['honda', 'yamaha', 'suzuki'，'ducati']

在列表特定的位置上插入

```python
motorcycles = ['honda', 'yamaha', 'suzuki']
print(motorcycles)

motorcycles.insert(0, 'ducati')
print(motorcycles)
```

输出

['honda', 'yamaha', 'suzuki']
['honda', 'yamaha', 'suzuki'，'ducati']

#### 从列表中删除数据

使用 del 语句删除元素

```python
motorcycles = ['honda', 'yamaha', 'suzuki']
print(motorcycles)

del motorcycles[0]
print(motorcycles)
```

输出

['honda', 'yamaha', 'suzuki']
['suzuki', 'ducati']

使用 pop() 删除元素

默认删除列表中最后一个元素

```python
motorcycles = ['honda', 'yamaha', 'suzuki']
print(motorcycles)

poped_motorcycles = motorcycles.pop()
print(motorcycles)
print(poped_motorcycles)
```

输出

['honda', 'yamaha', 'suzuki']
['honda', 'yamaha']
suzuki

删除列表特定位置的元素

```python
motorcycles = ['honda', 'yamaha', 'suzuki']
print(motorcycles)

poped_motorcycles = motorcycles.pop(0)
print(motorcycles)
print(poped_motorcycles)
```

输出

['honda', 'yamaha', 'suzuki']
['yamaha', 'suzuki']
honda

根据值删除元素

```python
motorcycles = ['honda', 'yamaha', 'suzuki']
print(motorcycles)

motorcycles.remove('honda')
print(motorcycles)
```

输出

['honda', 'yamaha', 'suzuki']
['yamaha', 'suzuki']

### 管理列表

#### 使用 sort() 方法对列表进行永久排序

默认按照字典序升序的方式进行排序

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.sort()
print(cars)
```

输出 ['audi', 'bmw', 'subaru', 'toyota']

传入 reverse=True 按照字典序降序的方式进行排序

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.sort(reverse=True)
print(cars)
```

输出 ['toyota', 'subaru', 'bmw', 'audi']

#### 使用 sorted() 函数对列表进行临时排序

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
print(sorted(cars))
```

输出 ['audi', 'bmw', 'subaru', 'toyota']

注意：不会改变原始列表

#### 反向打印列表

使用 reverse() 方法

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.reverse()
print(cars)
```

输出 ['subaru', 'toyota', 'audi', 'bmw']

#### 确定列表的长度

使用 len() 函数

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
len(cars)
```

输出 ['subaru', 'toyota', 'audi', 'bmw']
