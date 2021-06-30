---
title: 猿人学练习平台 1——js加密——无混淆js加密——简单
date: 2021-06-30 14:52:43
tags: [js逆向, 逆向思路, 猿人学]
categories: 猿人学
---

[题目地址][题目地址]
## 分析
### 查看请求信息
1. 统计数量，那么需要得到每一页的数据，先看看每一页的数据请求流程
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210630150211_1.png)

1. 会发现每次翻页会有2个请求，依次点开看看
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210630150630_2.png)

1. 发现有个 `safe` 参数是在变化的
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210630150826_3.png)

1. cookie 中有个参数编码看起来有点问题,肯定没法直接用
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210630150936_4.png)

1. 依据浏览器工具查看一下, 得到正常编码字符
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210630151311_5.png)

### 分析动态参数
1. safe搜索
> 先使用搜索看看加密逻辑，很容易就可以搜到（如果没搜出来，使用`ctrl+shift+r`强刷新再搜索试试）
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210630151902_6.png)

> 看看代码
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210630152000_7.png)

> 把上边三行复制下来，执行看看，对比看，应该是对的
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210630152041_8.png)


## 代码验证
### 用 python 还原 safe 参数算法
```python
# var a = '9622';
a = '9622'

# var timestamp = String(Date.parse(new Date()) / 1000);
timestamp = str( int(time.time()) )

# var tokens = hex_md5(window.btoa(a + timestamp));
## 1. window.btoa(a + timestamp)
### 直接这么做有个问题，就是会被 b'' 包住
In [1]: base64.b64encode( ( a + timestamp ).encode('utf-8') )
Out[2]: b'OTYyMjE2MjUwMzg5NzI='
### 所以稍微改动一下，就正常了
In [3]: str( base64.b64encode( ( a + timestamp ).encode('utf-8') ),'utf-8' )
Out[4]: 'OTYyMjE2MjUwMzg5NzI='

## 2. python3 使用 md5 需要指定编码，不然会报错 报错为： Unicode-objects must be encoded before hashing
btoa_str = str( base64.b64encode( ( a + timestamp ).encode('utf-8') ),'utf-8' )
tokens = hashlib.md5( btoa_str.encode(encoding='utf-8') ).hexdigest()

```
### 一些意外情况
```python
# cookie中有一个上边提取出来的值，可能会报编码问题
'enable_GG思密达': 'true'
cookies2 = {
    'enable_GG思密达': 'true',
    '__yr_token__': 'b301cDHZoDT************************************RQR8WAZYF3ZrXAZPABUMG3kJWQNBYRY%3D',
    'Hm_lvt_337e99a01a907a08d00bed4a1a52e35d': '1623315686',
    'vaptchaNetwayTime': str(int(time.time())),
    'vaptchaNetway': '1',
    'sessionid': '9g1elieze5**********7n41e9kvw',
    'no-alert': 'true',
    'Hm_lpvt_337e99a01a907a08d00bed4a1a52e35d': '1625032842',
}
# 报错
UnicodeEncodeError: 'latin-1' codec can't encode characters in position 282-284: ordinal not in range(256)

# 经过验证 修改一下编码就可以
cookies2 = {
    'enable_GG思密达'.encode("utf-8").decode("latin1"):'true',
    '__yr_token__': 'b301cDHZoDT************************************RQR8WAZYF3ZrXAZPABUMG3kJWQNBYRY%3D',
    'Hm_lvt_337e99a01a907a08d00bed4a1a52e35d': '1623315686',
    'vaptchaNetwayTime': str(int(time.time())),
    'vaptchaNetway': '1',
    'sessionid': '9g1elieze5**********7n41e9kvw',
    'no-alert': 'true',
    'Hm_lpvt_337e99a01a907a08d00bed4a1a52e35d': '1625032842',
}
```

## 完整代码，修改为自己的cookie信息
```python

#%%
import requests
from pprint import pprint
import time
from jsonpath import jsonpath
import json
import hashlib
import base64

count_zhao = 0
for page in range(1,3):
    cookies1 = {
        'Hm_lvt_337e99a01a907a08d00bed4a1a52e35d': '1623315686',
        'vaptchaNetwayTime': str(int(time.time())),
        'vaptchaNetway': '1',
        'sessionid': '9g1elieze************41e9kvw',
        'no-alert': 'true',
        'Hm_lpvt_337e99a01a907a08d00bed4a1a52e35d': '1625032842',
    }
    headers1 = {
        'Connection': 'keep-alive',
        'Content-Length': '0',
        'Pragma': 'no-cache',
        'Cache-Control': 'no-cache',
        'sec-ch-ua': '" Not;A Brand";v="99", "Google Chrome";v="91", "Chromium";v="91"',
        'sec-ch-ua-mobile': '?0',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36',
        'Accept': '*/*',
        'Origin': 'https://www.python-spider.com',
        'Sec-Fetch-Site': 'same-origin',
        'Sec-Fetch-Mode': 'cors',
        'Sec-Fetch-Dest': 'empty',
        'Referer': 'https://www.python-spider.com/challenge/1',
        'Accept-Language': 'zh-CN,zh;q=0.9',
    }

    response1 = requests.post('https://www.python-spider.com/cityjson', headers=headers1, cookies=cookies1, verify=False)
    pprint(response1.text)

    # safe = hashlib.md5(str(base64.b64encode(('9622'+str(int(time.time()))).encode('utf-8')),'utf-8').encode(encoding='utf-8')).hexdigest()
    a = '9622'
    timestamp = str( int(time.time()) )
    btoa_str = str( base64.b64encode( ( a + timestamp ).encode('utf-8') ),'utf-8' )
    tokens = hashlib.md5( btoa_str.encode(encoding='utf-8') ).hexdigest()
    
    cookies2 = {
        'enable_GG思密达'.encode("utf-8").decode("latin1"):'true',
        '__yr_token__': 'b301cD************************AZYF3ZrXAZPABUMG3kJWQNBYRY%3D',
        'Hm_lvt_337e99a01a907a08d00bed4a1a52e35d': '1623315686',
        'vaptchaNetwayTime': str(int(time.time())),
        'vaptchaNetway': '1',
        'sessionid': '9g1elie***************41e9kvw',
        'no-alert': 'true',
        'Hm_lpvt_337e99a01a907a08d00bed4a1a52e35d': '1625032842',
    }
    headers2 = {
        'Connection': 'keep-alive',
        'Pragma': 'no-cache',
        'Cache-Control': 'no-cache',
        'sec-ch-ua': '" Not;A Brand";v="99", "Google Chrome";v="91", "Chromium";v="91"',
        'Accept': 'application/json, text/javascript, */*; q=0.01',
        'timestamp': str(int(time.time())),
        'X-Requested-With': 'XMLHttpRequest',
        'sec-ch-ua-mobile': '?0',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36',
        'safe': tokens,
        'Sec-Fetch-Site': 'same-origin',
        'Sec-Fetch-Mode': 'cors',
        'Sec-Fetch-Dest': 'empty',
        'Referer': 'https://www.python-spider.com/challenge/1',
        'Accept-Language': 'zh-CN,zh;q=0.9',
    }
    params2 = (
        ('page', str(page)),
        ('count', '14'),
    )

    response2 = requests.get('https://www.python-spider.com/challenge/api/json', headers=headers2, params=params2, cookies=cookies2, verify=False)
    response2_json = json.loads(response2.text)
    try:
        for i in jsonpath(response2_json,'$..infos')[0]:
            pprint(i)
            if '招' in i['message']:
                count_zhao += 1
    except Exception as e:
        print(e,response2_json)

print(count_zhao)


```


[题目地址]:https://www.python-spider.com/challenge/1