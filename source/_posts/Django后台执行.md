---
title: Django后台执行
date: 2021-05-19 17:00:05
tags: [Django, Linux]
categories: Web
---

## 让 django 在服务器挂起执行，不中断
> 在 服务器中启动 django 通常是
`python python manage.py runserver 0.0.0.0:8000`
>但是离开 ssh 连接的窗口 程序通常也会被终止，以下有两种 方法挂起执行
### 1. 以 nohup 不挂断执行
`nohup python python manage.py runserver 0.0.0.0:8000 &`
> 启动后 还会在当前目录产生 nohup.log 相当于日志记录的作用

### 2. screen
> 此方法针对，nohup 启动的项目 无法以 IP：Port 的方式访问api，也是更高级的用法
> * 1. 先检查是否安装了 screen
```shell
>>>screen -S 名称 
如果成功跳转到窗口 证明已安装，终端报错则，进行安装
>>>sudo apt install screen
```

> * 2. 安装之后 就以 创建新screen 并且执行命令的方式启动:
`>>>screen -S test python hello.py`

> * 3. 但是，有时候，我们的操作不仅仅这么简单，需要跳转路径且切换虚拟环境等等，
那么，就先写一个shell 脚本，帮助我们只用一行完成所有操作
如
```shell
cd /home/tangsong/  #切换路径 
source venvo/bin/activate  # 激活虚拟环境 ps：没有则跳过
cd LetGo/
python3 manage.py runserver 0.0.0.0:9000  # 启动项目
```
> * 4. 然后 用screen 命令启动
`screen -S test ./start.sh`
`注意 ： start.sh 启动脚本，一定要放在，窗口创建的初始目录下`