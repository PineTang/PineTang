---
title: 如何过最新5秒盾，也就是cloudflare
date: 2021-04-20 10:49:46
tags: [爬虫,反爬]
categories: 反爬
---

## 本测试在2021-04-20 10:49:46, 亲测可行

> 利用pypi找到包，安装后即可使用，[安装地址及说明][1]
```python
In [2]: import cloudscraper
   ...:
   ...: scraper = cloudscraper.create_scraper()  # returns a CloudScraper instance
   ...: # Or: scraper = cloudscraper.CloudScraper()  # CloudScraper inherits from requests.Session
   ...: print(scraper.get("https://www.aspi.org.au/search?f%5B0%5D=type%3Areport&sort_by=field_publication_date_common&
   ...: search_api_fulltext=&d%5Bmin%5D=&d%5Bmax%5D=&topics=&field_author_common=&field_program_tags_common=All").text)
   ...:
```

  [1]: https://pypi.org/project/cloudscraper/