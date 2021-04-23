---
title: scrapy 的POST和GET请求
date: 2021-04-16 15:03:04
tags: [scrapy请求方式]
categories: Scrapy
---
## scrapy构造POST和GET请求的方式

### POST
#### scrapy.FormRequest
```python
yeild scrapy.FormRequest(
    url='https://........', 
    method='post',  # 选择post
    dont_filter=True, # 关闭去重, 非必须
    formdata=data, # post 数据根据情况 选择是  dict类型: data / json字符串类型: json.dumps(data)
    callback=self.parse)
```
#### scrapy.Request
> 跟我们平时用get请求的方法一样
```python
yield scrapy.Request(
        method='POST',# 选择post
        url='https://...', 
        body=json.dumps(data), # 注意这里的key: body 就是指你需要请求的post data, 同样post 数据根据情况 选择是  dict类型: data / json字符串类型: json.dumps(data)
        headers=self.headers, # dict 类型
        callback=self.parse)
```

### GET
#### 一般GET请求
```python
yield scrapy.Request(url, 
                     headers=headers,
                     cookies=cookies,
                     callback=self.parse_article)
```
#### 有params的GET请求
```python
params = {
    'widen': '1',
    'tadrequire': 'true',
}
yield scrapy.Request(url='https://www....', 
                     method='GET',
                     body=json.dumps(params),
                     headers=FatherSpider.formatHeader(self.hd), 
                     callback=self.parse)
```