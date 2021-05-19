---
title: Python 错误合集
date: 2021-05-19 16:46:27
tags: [Python, 错误解决]
categories: Python
---

# Python3错误
## 字符编码问题
### 1. \u4567\u753r5转换中文
> 向接口模拟请求时，你可能会收到如下响应
```python
import requests
url = "xxxxx"
response = requests.get(url=url)
print(response.content)
>>> \u4567\u753r5
```
> 一般情况，只需要encode编码就行了
```python
str_content = response.content.encode("utf-8") #或者 gbk
print(str_content)
OUT:中文内容
```
> 但在Python3 中 你可能会遇到如下情况
`\\u4567\\u753r5`
> 这时我们需要先编码，再解码
```python
str_content = response.content.encode("utf-8").decode("unicode_escape") 
print(str_content)
>>> 中文内容
```

## 字符串匹配
### 1. 字符串 == 
> 在Windows下 通过 xpath 获取文本内容，返回得到字符串,如：
`https://www.qujiaosuo.pro/`
> 我需要在找到此链接时，进入站内，达到更多操作，用 基本的判断，并不能成功,如==判断
```python
if getstr == "https://www.qujiaosuo.pro/":
    print("找到链接")
```
> 但是匹配一直不成功，多番分析发现问题是 获取的文本字符串末尾 被系统自动追加 '\0',验证看看：
```python
"打印获取的字符串长度"
print(len(getstr))
>>> 27
"打印需要的目标长度"
print(len("https://www.qujiaosuo.pro"))
>>> 26
```
> 所以，只要去掉末尾的 '\0' 就好了,在此对比就可以了,这里简单粗暴处理下
```python
getstr = getstr[:-1]
print(len(getstr))
>>> 26
```
---
# pip错误
## pip更新
### 1. linux下更新pip时， 出现错误
```python
Traceback (most recent call last):
	File "/usr/bin/pip3", line 9, in <module>
		from pip import main
ImportError: cannot import name 'main'
```
> 解决方法：
```python
# vim /usr/bin/pip   (若是pip3，就改为pip3)
#将导包改为如下即可：
from pip._internal import main
```