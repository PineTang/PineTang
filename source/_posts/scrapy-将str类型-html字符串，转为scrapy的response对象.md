---
title: scrapy 将str类型 html字符串，转为scrapy的response对象
date: 2021-04-16 15:13:41
tags: [scrapy对象的使用]
categories: Scrapy
---
![img](http://image.wufazhuce.com/FmYSTDrtrkf8Kbg8K9KbnFfejW1T)
### 以下内容为，如何将str类型的 html文本转为 scrapy的response对象
> 这么做的好处是, 转为scrapy自身的response对象, 
就可以使用response.css(),
就可以使用response.xpath(),
就可以使用response.json(),
等方法, 当然你也可以使用 lxml 进行处理, 我这里是为了方便统一大量的爬虫书写规范

```python
from scrapy.http import HtmlResponse
# content为待转换的html文本，如：'<p>balabalbablabla</p>'
html = HtmlResponse(
            url=response.url, # 设置一个url, 推荐当前使用方式
            body=content.encode('utf-8') # 选择编码格式
        )
# 现在 html 就是我们转换后的 response类型对象, 可以进行 html.css(), html.xpath()等操作了
```