---
title: js逆向的一些思路尝试，websocket调用
date: 2021-05-17 17:03:01
tags: [js逆向]
categories: 逆向思路
---

## 引发
> 对于web爬虫遇到的反爬，一般少不了扣js，对于一些复杂的js，抠出来也不方便还原，很费时间；
> 由此，可以用webdriver/jsdom/nodejs去执行js，由python去调用，但上述几个方法都有弊端。
> 比如资源消耗，非真实浏览器被检测的问题，主要是伪装真实浏览器的麻烦程度，那我想，为什么不干脆由真实浏览器去做这些事呢。

## 思路
> 如果我有一个server端作为调用参数任务的下发者，那么在真实浏览器构建一个client端去接收任务，并返回结果就可以达到参数调用即获取；
> 这样我不用关心检测，也不用关心目标网站是否更新了内部算法，只要他暴露的参数计算函数名没变，我就可以调用，省了很多头发啊

## 简易的demo
### * server
```python
import asyncio
import websockets
import json

async def echo(websocket, path):
    async for message in websocket:
        
        await return_task(message)
        await send_task(websocket)
        print("获得连接：",message)
        

async def send_task(websocket):
    task = input("输入任务名：")
    await websocket.send(task)

async def return_task(message):
    print(message)
    recv_json = json.loads(message)
    if recv_json['message'] == "params":
        print("获得参数",message)
    

asyncio.get_event_loop().run_until_complete(websockets.serve(echo, '127.0.0.1', 4200))
asyncio.get_event_loop().run_forever()
```
> 在真实场景中，需要把 send_task()中的input改成自己的 任务生产消息队列，进行获取任务，每个任务包含任务Id，任务操作类型，让客户端知道干什么
> 在return_task()中获取客户端返回的结果，把获得的参数信息，发到 结果消费队列，供爬虫使用，爬虫根据id等到自己的参数就去执行

### * client
```javascript
js client 获取cookies示例
function WebSocketTest() {
    if ("WebSocket" in window) {
        // 打开一个 web socket
        var ws = new WebSocket("ws://127.0.0.1:4200");
        // 连接建立后的回调函数
        ws.onopen = function () {
            // Web Socket 已连接上，使用 send() 方法发送数据
            let obj = {};
            obj.message = 'task';
            obj.name = '获取ck';
            ws.send(JSON.stringify(obj));
            console.log("获得长连接!")
        };

        // 接收到服务器消息后的回调函数
        ws.onmessage = function (evt) {
            console.log(evt)
            var received_msg = evt.data;
            let obj = {};
            obj.message = 'params';
            obj.name = received_msg;
            obj.value = document.cookie;
            ws.send(JSON.stringify(obj));
        };
        // 连接关闭后的回调函数
        ws.onclose = function () {
            // 关闭 websocket
            console.log("连接已关闭...");
        };
    } else {
        // 浏览器不支持 WebSocket
        alert("您的浏览器不支持 WebSocket!");
    }
}
```
> 以上是一个获取cookie的demo，可以再此基础上扩展自己的功能，根据server发送来的任务，调用不同的方法，操作tab页，发送请求等等
> 将结果和任务Id，封装后交给server去处理，比如hook XHR请求，改写方法，将所有的请求信息拿下来，但不发送，交由自己的爬虫去带参请求

> 以上只是一个思路，想要完善还需要更加详细的了解WebAPI，以及如何并发等等问题，一但功能丰富起来，就是一个web逆向通杀方案