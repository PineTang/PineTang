---
title: python中求列表的 交、并、差集
date: 2021-06-17 10:51:02
tags: [Python,List]
categories: Python
---

> 有如下两个列表
```python
a=[1,2,3]
b=[1,2,4]
```

## 求交集
### 1. 两个列表共有部分
```python
In [1]: list(set(a) & set(b))
Out[2]: [1, 2]
```

## 求并集
### 1. 两个列表合并
```python
In [1]: list(set(a) | set(b))
Out[2]: [1, 2, 3, 4]
```

## 求差集
### 1. 列表 a 中有，b 中没有的部分
```python
In [1]: list(set(a).difference(set(b))) # 同 list(set(a)-set(b))
Out[2]: [3]
```
### 2. 列表 b 中有，a 中没有的部分
```python
In [3]: list(set(b).difference(set(a))) # 同 list(set(b)-set(a))
Out[4]: [4]
```