---
title: 2——js加密——简易动态js加密解析——简单
date: 2021-07-01 14:06:07
tags: [js逆向, 逆向思路, 猿人学]
categories: 猿人学
---

[题目地址][题目地址]
## 分析
### 查看请求信息
1. 刷新页面会，打开控制台会无限 debugger，把它过掉
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210701143216_1.png)

1. 然后再次刷新就正常了
> 经过尝试，会发现在第一个请求的时候，`cookie` 中的 `sign` 值就已经在请求头了，所以应该是本地计算完成的，
> 接下来就是找到计算的位置

### 定位
> 一般我会用 XHR 断点，但是这里不属于 XHR 请求方式，所以直接下 script事件监听断点
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210701144107_2.png)

> 为了方便查看，断点下好后，先去 `cookies` 里把 `sign` 给删掉，
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210701144359_3.png)

> 然后就可以刷新页面开始调试了，就这样走一次 F8 看一下cookie 变化（也可以直接hook cookie 就没这么麻烦），留意每次调试的文件有用
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210701144854_4.png)

> 在经过一次卡顿后，出现了目标，并且上方调试时走过的文件也没了，
> 这样我们没法回到上一次（堆栈也没有啊），所以刚刚提示的留意就派上用场了
> 知道在什么位置了，那么就重新操作一遍，删除 cookie sign 刷新页面
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210701145226_5.png)

> 经过再次调试，来到了cookie变化前一个断点文件
> 格式化之后，搜一下cookie，看了看，因该就是这块的了
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210701150119_6.png)

> 下个断点，看一下，这就是需要的 `sign` 了，到这里，答案已经可以出来了，把 `c` 的值改为题目的时间戳, 再依次执行就可以了
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/20210701150358_7.png)

> 不过为了学习，我们还是把它用`python`还原一下,下边先对这几行代码进行逐步分析

### 分析cookie参数
1. 分析逻辑
```javascript
var c = new Date()['valueOf'](); // 1625118526378   
token = window["btoa"]( 
    a["eheZE"]( 
        a["VezOH"], // "aiding_win"
        a["gFoHV"](String, c) // "1625108819386"   python:str(c)
    ) // "aiding_win1625108819386"    "aiding_win" + str(c)    python: "aiding_win"+ str(c) 
); // 对 "aiding_win1625108819386" 进行 base64 编码
md = a['XQoGX'](
        hex_md5, 
        window["btoa"](
        a['gNohH']( 
            a["VezOH"], // "aiding_win"
            a["sibsM"]( 
                String, 
                Math["round"]( 
                    a["xHDyr"](c, 0x3e8)  // 1625108819.386    python:c/1000
                )  // 对 1625108819.386 四舍五入取整 == 1625108819    python:round(c/1000)
            ) // "1625108819"  转字符串类型   js: String(1625108819)    python: str(round(c/1000))
        ) // "aiding_win1625108819"     "aiding_win"+"1625108819"   python: "aiding_win"+str(round(c/1000))
    ) // "YWlkaW5nX3dpbjE2MjUxMDg4MTk="       base64_str
);//   "1eb0ec7446090df575752a23a4511427"   对 base64 编码后的字符串进行 md5 编码     hex_md5(base64_str)   

document["cookie"] = a["ttTXc"]( 
    a["ttTXc"]( 
        a["vqcDR"]( 
            a["ITeIW"](
                a['DTGFR'](
                    a["Bxtms"](
                        a["RojwX"], //  "sign="
                        Math["round"](
                            a['xHDyr'](c, 0x3e8)  // 1625108819.386    python:c/1000
                        )// 对 1625108819.386 四舍五入取整，结果为：1625108819    python:round(c/1000)
                    ), // "sign=1625108819"     "sign="+ 1625108819
                    '~'
                ), // "sign=1625108819~"     "sign=1625108819"+'~'
                token
            ), //  "sign=1625108819~aiding_win1625108819386"     "sign=1625108819~"+ token
            '|'
        ),  //  "sign=1625108819~aiding_win1625108819386|"     "sign=1625108819~aiding_win1625108819386"+'|'
        md
    ),   //   "sign=1625108819~aiding_win1625108819386|1eb0ec7446090df575752a23a4511427"    "sign=1625108819~aiding_win1625108819386|"+ md 
    a["nZuLl"]  // "; path=/"
); //   "sign=1625108819~aiding_win1625108819386|1eb0ec7446090df575752a23a4511427; path=/"      "sign=1625108819~aiding_win1625108819386|1eb0ec7446090df575752a23a4511427"+"; path=/"

```

## 完整代码，python还原计算
```python
import base64
import hashlib
import time

# c = int(time.time()*1000)
c = 1587102734000
token = str( base64.b64encode( ( "aiding_win" + str(c) ).encode('utf-8') ),'utf-8' )
md = hashlib.md5( str( base64.b64encode( ( "aiding_win" + str( round( c/1000 ) ) ).encode('utf-8') ),'utf-8' ).encode(encoding='utf-8') ).hexdigest()
cookie = "sign=" + str( round( c/1000 ) ) + '~' + token + '|' + md + "; path=/"
print(cookie)
```


[题目地址]:https://www.python-spider.com/challenge/2