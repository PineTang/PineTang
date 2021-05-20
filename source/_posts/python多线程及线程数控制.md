---
title: python多线程及线程数控制
date: 2021-05-19 16:10:25
tags: [Python, 多线程]
categories: Python
---

## 在python中控制线程最大数
### 极简代码示例
```python
import threading
import time

def io_task(sema, taks_num):
    # 占用一个线程空位
    sema.acquire()

    print('调用线程{0}'.format(taks_num))
    # 模拟IO耗时
    time.sleep(2)

    # 任务完成，释放占用
    sema.release()

if __name__ == "__main__":
    # 设置最大线程数为2
    sema = threading.Semaphore(value=2)

    # 模拟任务数为10
    task_workers = 10

    # 开始任务
    for taks_num in range(task_workers):

        # 线程调用
        t = threading.Thread(target=io_task, args=(sema, taks_num,))
        t.start()
    
    # 等待所有线程结束，继续往下操作
    t.join()

    '''
        do soming
    '''
    print("所有任务完成！")

# 输出结果

调用线程0
调用线程1


调用线程2
调用线程3
...
...
所有任务完成！
```

### 个人理解
> 由以上程序及运行结果可以得知，
* 1. 首先需要设置线程最大任务数    `sema = threading.Semaphore(value=2)`
* 2. 线程调用并传入信号量


```python
t = threading.Thread(target=io_task, args=(sema, taks_num,))
t.start()
```


* 3. 在被调用方法 `io_task(sema, taks_num)` 中
进行线程资源占用申请 `sema.acquire()` 此方法会申请占用一个资源位置，最大可用线程数 -1 , 此方法需在 IO 操作前调用才能达到效果
线程资源释放 `sema.release()` 此方法会释放一个资源位置，最大可用线程数 +1 , 此方法需在 IO 操作后调用

