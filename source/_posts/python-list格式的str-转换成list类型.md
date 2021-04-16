---
title: 'python list格式的str, 转换成list类型'
date: 2021-04-16 10:13:59
tags: [Python, 字符串处理]
categories: Python
---
## 今天遇到一个list格式的str，我想重新转回list类型
> 待转换目标：
```python
In [172]: re.findall('"TemplateId":(.*?),',rsp.text)[0]
Out[172]: '["{652B0562-7D75-46D2-A6A6-C63CF833AE3A}"]'

In [173]: type(re.findall('"TemplateId":(.*?),',rsp.text)[0])
Out[173]: str
```

### 首先想到的是 list() 方法, 结果
```python
In [174]: list(re.findall('"TemplateId":(.*?),',rsp.text)[0])
Out[174]:
['[',
 '"',
 '{',
 '6',......]
```
> 很明显, 这不是我想要的结果


### 最后借助 json.loads 成功转换
```python
In [175]: json.loads(re.findall('"TemplateId":(.*?),',rsp.text)[0])
Out[175]: ['{652B0562-7D75-46D2-A6A6-C63CF833AE3A}']
```