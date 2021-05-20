---
title: python中字符串模板的使用
date: 2021-05-20 14:49:52
tags: [Python, 字符串处理]
categories: Python
---

## 在开发中，遇到多个字符串，动态更改其中不同位置的参数
```python
start_urls = ["https://csbaonline.org/about/media-center/P{动态参数}?filter=news",
              "https://csbaonline.org/about/news/P{动态参数}"]

# 需求
'''
    https://csbaonline.org/about/media-center/P1?filter=news
    https://csbaonline.org/about/news/P1

    https://csbaonline.org/about/media-center/P2?filter=news
    https://csbaonline.org/about/news/P2
'''
for page in range(10):
    for url in start_urls:
        url ??? 拆分拼接? ，这不够pythonic，比较简单那粗暴
```

> 可以使用 python string 中的 Template，制作字符串模板
```python
from string import Template

# 定义模板，需要动态变化的地方，使用 $变量名 的方式作为占位符
start_urls = [Template("https://csbaonline.org/about/media-center/P$p?filter=news"),
              Template("https://csbaonline.org/about/news/P$p")]

for page in range(10):
    for url in start_urls:
        # 给模板中的变量赋值，变量可以定义多个
        url = url.substitute(p=page+1)
```
> 看起来简洁、舒服多了...