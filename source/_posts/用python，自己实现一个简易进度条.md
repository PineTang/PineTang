---
title: 用python，自己实现一个简易进度条
date: 2021-05-24 15:11:41
tags: [Python]
categories: Python
---

## 用 Python 实现一个简易的进度条，不需要任何第三方包
> 直接上代码
```python
#-*- coding:utf-8 -*-
import time
import sys

task = 100 
for i in range(task):
    # 计算进度条字符串
    a = '*'*int( (i+1)/task*100 )+' '*int( 100-( (i+1)/task*100 ) )
    # 计算百分比
    b = str((i+1)/task*100)
    # 刷新缓冲区
    sys.stdout.flush()
    # 不换行输出
    print(f"\r[{a}] */- {b[:4]}%",end="")
    time.sleep(0.1)
```
> 效果如下


![img](https://gitee.com/PineKer/myfiles/raw/master/blog/202105241.png)

![img](https://gitee.com/PineKer/myfiles/raw/master/blog/202105242.png)