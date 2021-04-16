---
title: scrapy 重定向解决
date: 2021-04-15 16:02:50
tags: [Scrapy,重定向]
categories: 爬虫
---

## 只需要在每次构造请求前, 在meta中添加如下信息即可,settings中如果有默认user-agent，又非本次请求的网站头也建议注释掉
```python

yield scrapy.Request(
    url, 
    callback=self.parse, 
    dont_filter=True,# Scrapy是默认过滤掉重复的请求URL, 关闭它
    meta={ 
        'dont_redirect': True, # 关闭重定向
        'handle_httpstatus_list': [301,302] # 接受的响应 code
    }
)
```