---
title: headers签名逆向
date: 2021-12-13 14:57:54
tags: [js逆向, 逆向思路]
categories: 反爬
---

## 分析
### 查看请求相关信息

1. 查看请求流程
> 变化的参数有两个，主要是 authorization
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_1.png)

> 先看看调用堆栈信息，可以发现主要是 ROHReact.js 和 VM
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_2.png)

> 先去搜一下关键字 authorization；可以发现一个是 string 类型的一段js代码；另一个是调用堆栈里的那个js文件，
> 先从字符串代码开始跟踪，个人也是人为可能性大一点
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_3.png)

> 搜索这段代码的变量名，可以找到 使用 eval 执行他的地方，打赏断点，跟进去调试
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_4.png)

> 可以看到成功断住了，单步跟下去
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_5.png)

> 跟进去之后发现了关键字
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_6.png)

> 并且经过搜索找到了下方的引用位置，打上断点，执行到这个位置，发现赋值就在这里；
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_7.png)

### 分析动态参数
1. 扣出代码
> 把刚才跟进去的那个文件代码，全部复制到本地, 并在 headers 赋值后的地方输出一下，
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_8.png)

2. 调试
> 整理一下，主动调用这段代码，并从 debug 中把需要的 config 参数复制出来;
> 然后执行发现报错 Cannot read property 'assertDefined' of undefined
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_9.png)

![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_10.png)

3. 继续扣代码
> 选中目标代码，然后点进去，然后添加到本地文件中
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_11.png)

4. 再次调试
> 将复制进来的代码整理，去掉重复的变量声明字段，再次执行，出现 document is not defined
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_12.png)

5. 排查错误
> 发现只有一处引用，并且只是获取了一下 hostname，那么直接写死就完事, 把 document 的地方注释掉
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_13.png)

![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_14.png)

6. 使用 node 的 CryptoJS
> 再次执行缺少加解密库，可以直接安装 `npm install crypto-js` , 然后再引用就 OK
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_15.png)

7. 收尾
> 再次执行出现 axios is not defined 错误，搜索定位到返回值是 `return axios(signedRequest);`，
> 我们不需要封装请求，毕竟上方可以看到，已经成功输出 headers 中我们需要的值，所以 直击返回就好 `return signedRequest`
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_16.png)

> 完结撒花
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211213151406_17.png)


### 分析总结
> 根据搜索定位到关键参数位置，发现对方使用 eval 去执行的相关代码，找到 eval 执行位置，断点跟进去，确认参数生成位置，扣代码
> 根据扣下来的代码，补环境，补参数，整理不需要的部分（axios， document）
> 安装加密库 crypto-js
> 为了方便使用 python 调用，将 js 文件倒数第二行改为 `var signedRequest = awsSigV4Client.makeRequest`

## 代码验证
```python
import execjs
import os
import requests
BASE_DIR = os.path.dirname(os.path.realpath('__file__')).replace('\\', r'/')

with open(f"{BASE_DIR}/blog.js", 'r') as f:
    js_code = f.read()

js_env = execjs.get()
js_complied = js_env.compile(js_code)
request = {
    "verb": "GET",
    "path": "/roh/army/sbv",
    "headers": {},
    "queryParams": {
        "type": "Person",
        "ID": "10015",
        "version": "2.00"
    }
}
rst = js_complied.call('signedRequest', request)

headers = {
    # 'authority': '2te05r2hbk.execute-api.us-east-1.amazonaws.com',
    'pragma': 'no-cache',
    'cache-control': 'no-cache',
    'sec-ch-ua': '" Not A;Brand";v="99", "Chromium";v="96", "Google Chrome";v="96"',
    'accept': 'application/json',
    'authorization': 'AWS4-HMAC-SHA256 Credential=AKIAQJFZ7KCKQ5RKEVFL/20211210/us-east-1/execute-api/aws4_request, SignedHeaders=accept;host;x-amz-date, Signature=68b3238747d919ac607c2db288bb893d784b91d2cce93d3ca973405281976837',
    'x-amz-date': '20211210T095518Z',
    'sec-ch-ua-mobile': '?0',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.55 Safari/537.36',
    'sec-ch-ua-platform': '"macOS"',
    'origin': 'https://army.togetherweserved.com',
    'sec-fetch-site': 'cross-site',
    'sec-fetch-mode': 'cors',
    'sec-fetch-dest': 'empty',
    'referer': 'https://army.togetherweserved.com/',
    'accept-language': 'zh-CN,zh;q=0.9',
}

params = (
    ('ID', '10015'),
    ('type', 'Person'),
    ('version', '2.00'),
)
headers['authorization'] = rst['headers']['Authorization']
headers['x-amz-date'] = rst['headers']['x-amz-date']
print(headers)

response = requests.get('https://2te05r2hbk.execute-api.us-east-1.amazonaws.com/roh/army/sbv', headers=headers, params=params)
response.json()
```