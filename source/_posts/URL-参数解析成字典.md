---
title: URL 参数解析成字典
date: 2021-08-12 15:19:13
tags: [Python, urllib]
categories: Python
---

> 讲 URL 中的查询字符串参数解析成字典
url如 `https://bipartisanpolicy.org/all-content/?policy_areas%5B%5D=Corporate+Governance&sort=date`

```python
from urllib.parse import urlencode, parse_qs, urlparse

url = "https://bipartisanpolicy.org/all-content/?policy_areas%5B%5D=Corporate+Governance&sort=date"
query_url = urlparse(url).query
parse_qs_url = parse_qs(query_url)

dict([(k,v[0]) for k,v in parse_qs_url.items()])

# 输出
{'policy_areas[]': 'Corporate Governance', 'sort': 'date'}
```

> 以上操作可以合并为
```python

url = "https://bipartisanpolicy.org/all-content/?policy_areas%5B%5D=Corporate+Governance&sort=date"
dict([(k,v[0]) for k,v in parse_qs(urlparse(url).query).items()])

# 输出
{'policy_areas[]': 'Corporate Governance', 'sort': 'date'}
```