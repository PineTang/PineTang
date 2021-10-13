---
title: jsl的cookie逆向，标准案例
date: 2021-10-13 09:52:52
tags: [js逆向, 逆向思路, jsl]
categories: 反爬
---

## 分析
### 查看请求相关信息

1. 查看请求流程
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_1.png)

![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_2.png)

![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_3.png)

![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_4.png)

![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_5.png)

### 分析动态参数
1. 将第一次响应内容的 js 代码全部复制下来，然后去 nodejs 环境跑一下
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_6.png)

2. 补头
> 可以看到有报错，那么接下来就是把所有的报错解决掉（补环境）
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_7.png)

> 根据上图调整，已经没有报错了，然后打印一下 document 发现 __jsl_clearance_s 已经可以拿到了
```javascript
document = {
    cookie: ""
}

location = {
    href:"",
    pathname:"",
    search:""
}

从第一次响应复制的 js 代码

console.log(document)

```

> 但是任务并没有结束，可以看到第二次请求，带上了 cookie 的两个参数仍然是失败的，
> 看一下他的响应内容
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_8.png)

> 可以看到相应内容还是一段 js 代码，并且比之前的更加复杂，先不做任何处理，直接放进现有补头的环境中执行，
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_13.png)

> 有新的报错，我们需要重复之前的操作进行补头，然后又出现了新的报错类型
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_9.png)

> userAgent这个属性，并不是直接在 window 下的，而是在 window.navigator.userAgent, 这里给补一个浏览器ua（随便复制一个就行）, 然后继续调试
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_10.png)

> 可以看到，已经没有报错了，但是并没有在全局变量中拿到值，根据分析 第二次响应内容种的代码，发现他使用了setTimeout 函数
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_11.png)
> setTimeout 函数的作用是将一个方法，在延时一段时间后执行，它的第一个参数就是需要执行的内容，这里可以将setTimeout 函数重写，然后将接收到的需要执行的方法，在全局中执行，那么 setTimeout 里 cookie 赋值的操作，就暴露给全局，就能拿到了
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20211013100136_12.png)

> 在添加重写的 setTimeout 后，就轻松的拿到了想要的结果，至此，js 逆向工作全部结束

### 分析总结
> 拿到数据总共需要请求三次该网站，第一次不做任何处理请求，获得 __jsluid_s 的值；
> 第二次需要根据第一次请求中response header中的 __jsluid_s，以及第一次请求响应中的 js 代码去计算 __jsl_clearance_s，然后带上这两个参数去请求；
> 第三次需要根据第一次请求中response header中的 __jsluid_s，以及第二次请求响应中的 js 代码去计算 __jsl_clearance_s，然后带上这两个参数去请求，就拿到了正确的html内容


## 代码验证
```python
import requests
import execjs
from scrapy.http import HtmlResponse

def formatHeader(hd):
    header = {}
    hd = hd.replace(" ", "").replace("\t", "").replace("        ", "")
    for i in hd.split("\n"):
        if i == "":
            continue
        kv = i.split(":")
        if len(kv) > 2:
            kv[1] = ':'.join(kv[1:])
        try:
            header[kv[0]] = kv[1]
        except:
            pass
    return header

def runJsCode(url, body):
    html = HtmlResponse(
        url=url, # 设置一个url, 推荐当前使用方式
        body=body # 选择编码格式
    )
    cookie_code = html.css('script::text').get()
    
    js_env = execjs.get()
    
    js_code = '''
        document = {
            cookie: ""
        }
        setTimeout = function(fuc,str){
            fuc()
        }
        window = {
            navigator: {
                userAgent:'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.71 Safari/537.36'
            }
        }
        location = {}
        '''+cookie_code+'''
        function getCookie(){
            return document.cookie
        }
    '''
    
    # 先编译js解密代码
    js_compiled = js_env.compile(js_code)
    # 执行js_func得到最终结果
    result = js_compiled.call('getCookie').split(';max')[0]
    print(result)
    return result


hd = '''
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
    Accept-Encoding: gzip, deflate, br
    Accept-Language: zh-CN,zh;q=0.9
    Cache-Control: no-cache
    Connection: keep-alive
    Cookie: ""
    Host: www.cnvd.org.cn
    Pragma: no-cache
    sec-ch-ua: "Chromium";v="94", "Google Chrome";v="94", ";Not A Brand";v="99"
    sec-ch-ua-mobile: ?0
    sec-ch-ua-platform: "Windows"
    Sec-Fetch-Dest: document
    Sec-Fetch-Mode: navigate
    Sec-Fetch-Site: none
    Sec-Fetch-User: ?1
    Upgrade-Insecure-Requests: 1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.71 Safari/537.36
'''
header=formatHeader(hd)
article_url = 'https://www.cnvd.org.cn/webinfo/list?type=2'
proxy = {'https': 'http://127.0.0.1:10809'}  # 我本地的翻墙代理


rsp = requests.get(article_url, proxies=proxy)
js_cookie = runJsCode('https://www.cnvd.org.cn', rsp.content)
header['Cookie'] = js_cookie
rsp = requests.get(article_url, proxies=proxy, headers=header)


js_cookie = runJsCode('https://www.cnvd.org.cn', rsp.content)
__jsluid_s = rsp.cookies['__jsluid_s']
header['Cookie'] = js_cookie+";__jsluid_s="+__jsluid_s
rsp = requests.get(article_url, proxies=proxy, headers=header)


js_cookie = runJsCode('https://www.cnvd.org.cn', rsp.content)
header['Cookie'] = js_cookie+";__jsluid_s="+__jsluid_s
rsp = requests.get(article_url, proxies=proxy, headers=header)
print(rsp.text)

```