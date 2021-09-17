---
title: 20210917-头条rpc获获取 _signature 参数
date: 2021-09-17 14:37:44
tags:
categories:
---


## 获取sekiro_web_client.js
https://sekiro.virjar.com/sekiro-doc/assets/sekiro_web_client.js

## JS 注入代码
```javascript
function guid() {
    function S4() {
        return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
    }
    return (S4() + S4() + "-" + S4() + "-" + S4() + "-" + S4() + "-" + S4() + S4() + S4());
}

var client = new SekiroClient("wss://sekiro.virjar.com/business/register?group=xxx&clientId=" + guid());

client.registerAction("clientTime", function (request, resolve, reject) {
    resolve("SekiroTest：" + new Date());
});

client.registerAction("_signature", function (request, resolve, reject) {
    var tt_url = window.atob(request['url'])
    // console.log("tt_url:   "+tt_url)
    resolve( window.byted_acrawler.sign.call(window.byted_acrawler,{url: tt_url})
    )
});

```

## 使用
1. 在浏览器打开 https://www.toutiao.com/
2. 将 sekiro_web_client.js 和 JS 注入代码 中的内容复制到浏览器控制台
3. 通过调用获取参数 https://sekiro.virjar.com/business/invoke?group=xxx&action=_signature&url=aHR0cHM6Ly93d3cudG91dGlhby5jb20vYXBpL3BjL2xpc3QvZmVlZD9vZmZzZXQ9MCZjaGFubmVsX2lkPTk0MzQ5NTQ5Mzk1Jm1heF9iZWhvdF90aW1lPTAmY2F0ZWdvcnk9cGNfcHJvZmlsZV9jaGFubmVs

> 说明：在 3. 中 url(需要获取的头条接口url，不带_signature参数) 参数为类似于 https://www.toutiao.com/api/pc/list/feed?offset=0&channel_id=94349549395&max_behot_time=0&category=pc_profile_channel 的base64编码