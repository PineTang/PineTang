---
title: Frida获取微信公众号推送文章
date: 2022-02-16 10:14:59
tags: [App逆向]
categories: Frida
---


## frida hook js逻辑 
> 经过借鉴各位前辈的文章，以及自行验证，知道了需要 Hook 的地方
```javascript
Java.perform(function () {
    // Hook插入数据库
    var SQLiteDatabase = Java.use('com.tencent.wcdb.database.SQLiteDatabase');
    var Set = Java.use("java.util.Set");
    var ContentValues = Java.use("android.content.ContentValues");
    var xml = Java.use("com.tencent.mm.sdk.platformtools.SemiXml");
    // 通过 fastjson 打印 json 数据
    Java.openClassFile('/data/local/tmp/fastjson.dex').load();
    console.log("加载外部 dex 完成")
    var JSONObject = Java.use('com.alibaba.fastjson.JSONObject');
    console.log("导入外部 dex 对象成功")

    SQLiteDatabase.insertWithOnConflict.implementation = function (arg1, arg2, arg3, i) {
        // 调用函数本身，不影响正常执行
        var ret = this.insertWithOnConflict.call(this, arg1, arg2, arg3, i);
        // 看一下参数信息
        var values = Java.cast(arg3, ContentValues);
        var sets = Java.cast(values.keySet(), Set);
        var arr = sets.toArray().toString().split(",");
        send(arr.toString())
        for (var i = 0; i < arr.length; i++){ 
            var key = arr[i];
            var value = values.get(key);
            if(key === "content"){
                var str_ = xml.decode(value)
                var res = JSONObject.toJSONString(str_);
                console.log("content xml: ")
                console.log(res);
                // 向 python 端发送
                send(res);
            }
        }
        console.log("____________________________________");
        return ret;
    };

});
```

## frida python 模板
> 这个都差不多，我这里是用的 WiFi 远程连接的设备，数据线自行更改 usb 方式
```python
import frida, sys
jscode = open("./fridahook.js", encoding='utf-8').read()

def on_message(message, data):
    # 接收 js 发送的信息
    if message['type'] == 'send':
        print("py message: ")
        print(message['payload'])

# 要Hook的软件包名
process = frida.get_remote_device().attach('com.tencent.mm')
script = process.create_script(jscode)
script.on('message', on_message)
print('运行完毕！')
script.load()
sys.stdin.read()

```

## 制作外部 dex 供 frida 使用
> 上面用到了 fastjson 去输出 json 数据，如果原 App 里没有，想使用自己熟悉的包，就可以自己制作一个 dex 从外部加载使用
1. [下载](https://repo1.maven.org/maven2/com/alibaba/fastjson/1.2.76/fastjson-1.2.76.jar) fastjson 的 jar 包 
2. 制作 dex
> 将下载的文件名改为fastjson.jar
将jar包移动到 Android SDK的目录：
windows：`C:\Users\用户名\AppData\Local\Android\Sdk\build-tools\版本号`
mac：`/Users/tangsong/Library/Android/sdk/build-tools/版本号`
> 进入到目录，输入命令 `dx --dex --output=fastjson.dex fastjson.jar`
当前目录会生成一个 `fastjson.dex` 的文件，将这个文件 push 到手机
通过 `adb shell` 进入手机，将 push 进来的 dex 文件 移动到 `/data/local/tmp/` 目录下（需要root权限）
然后修改文件权限 `chmod 777 fastjson.dex`

## 当有公众号推文的时候，就可以看到控制台的打印