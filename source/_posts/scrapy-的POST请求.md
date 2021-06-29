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

## 请求参数设置方法
### GET请求 查询字符串
#### 1. 使用FormRequest 

```python
start_urls = ['https://careers.tencent.com/tencentcareer/api/post/Query?']

def start_requests(self):
    for url in self.start_urls:
        params = {
            "timestamp": str(int(time.time() * 1000)),
            "countryId": "",
            "cityId": "",
            "bgIds": "",
            "productId": "",
            "categoryId": "",
            "parentCategoryId": "",
            "attrId": "",
            "keyword": "",
            "pageIndex": "1",
            "pageSize": "10",
            "language": "zh-cn",
            "area": "cn"
        }
        yield scrapy.FormRequest(url, 
                                method="GET", 
                                formdata=params)
```

#### 2. 使用urlencode

```python
from urllib.parse import urlencode
...
start_urls = ['https://careers.tencent.com/tencentcareer/api/post/Query?']

def start_requests(self):
    for url in self.start_urls:
        params = {
            "timestamp": str(int(time.time() * 1000)),
            "countryId": "",
            "cityId": "",
            "bgIds": "",
            "productId": "",
            "categoryId": "",
            "parentCategoryId": "",
            "attrId": "",
            "keyword": "",
            "pageIndex": "1",
            "pageSize": "10",
            "language": "zh-cn",
            "area": "cn"
        }
        url = url + urlencode(params)
        yield scrapy.Request(url,
                             method="GET")
```

### 请求 payload (GET)
```python
import json

payload= {
    "page": 1, 
    "size": 15, 
    "searchKeys": ["companyNameCN", "companyNameEN"],
    "isSearch": False,
    "selectAndMap": {
        "categoryId": [id], 
        "boothAreaSearch": [], 
        "boothNumber": []
    },
    "orderModel": {
        "order": "asc", 
        "properties": ["companyNamePinyin"]
    },
    "selectOrMap": {
        "isPovertyAlleviation": [], 
        "isFirstJoin": [], 
        "isContinuousJoin": [],
        "isBrand": [],
        "exhibitorType": [], 
        "productTrait": [], 
        "isCfWinner": [], 
        "tradeTypes": [],
        "isGreenAward": [],
        "isInvitationAward": []
    }, 
    "searchValue": ""
}

yield scrapy.Request(url, 
                     method="GET", 
                     body=json.dumps(payload) )

```

### POST请求 参数
#### 字符串格式表单 (Tue Jun 29 14:01:10 2021)
![例如](https://gitee.com/PineKer/myfiles/raw/master/blog/20210629_scrapy_post_params.png)
> 直接使用 `json.dumps(params_data)` 不符合格式要求，所以采用 `urlencode`
```python

params_data = [
    ('page', str(self.the_page)),
    ('view_name', 'defence_news_list_beta'),
    ('view_display_id', 'panel_pane_list3x3_all'),
    ('view_args', ''),
    ('view_path', 'all-news'),
    ('view_base_path', ''),
    ('view_dom_id', self.params_view_dom_id),
    ('pager_element', '0'),
    ('ajax_html_ids[]', 'global_nav_main_wrapper'),
    ('ajax_html_ids[]', 'global_nav_sitemap_responsive'),
    ...
    ...
    ('ajax_html_ids[]', 'edit-field-related-curated-topic-target-id'),
    ('ajax_html_ids[]', 'edit-submit-defence-news-list-beta'),
    ('ajax_html_ids[]', 'edit-reset'),
    ('ajax_page_state[theme]', 'defencenewsalpha'),
    ('ajax_page_state[theme_token]', jsonpath(Drupal_settings, '$..theme_token')[0]),
    ('ajax_page_state[css][modules/system/system.base.css]', '1'),
    ...,
    ('ajax_page_state[js][sites/default/themes/custom/bootstrap/js/misc/ajax.js]', '1'),
]
post_data = urlencode(params_data)
yield scrapy.Request(
    url=url, 
    method='post',  # 选择post
    headers=self.headers,
    dont_filter=True, # 关闭去重
    body=post_data, # post 数据根据情况 选择是  dict类型: data / json字符串类型: json.dumps(data)
    callback=self.parse_page)
```