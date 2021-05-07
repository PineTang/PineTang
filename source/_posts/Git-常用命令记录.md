---
title: Git 常用命令记录
date: 2021-05-07 15:17:16
tags: [git]
categories: Git
---

## git push 相关
### 将本地指定分支，推送到远程指定分支
```shell
git push 远程主机名 本地分支名:远程分支名
```
> **远程主机名**
    *1.可以直接填git地址*
    选择一种即可
    http: `https://XXX.com/XXX/XXX.git`
    ssh: `git@XXX.com:XXX/XXX.git`
    *2.通过命令设置*
    `git remote add 远程主机名 url`
### 将当前分支，推送到远程服务器
```shell
$ git remote show origin
* remote origin
  Fetch URL: https://XXX.com/XXX/XXX.git
  Push  URL: https://XXX.com/XXX/XXX.git
  HEAD branch: master
  Remote branches:
    hexo   tracked
    master tracked
  Local branch configured for 'git pull':   # 默认pull的分支
    XXX1 merges with remote XXX1
    XXX2 merges with remote XXX2
  Local ref configured for 'git push':   # 默认push的分支
    XXX1 pushes to XXX1 (up to date)
    XXX2 pushes to XXX2 (up to date)
```
> 根据当前本地所在分支，直接git push/pull ，会根据以上默认配置进行push/pull
$ git branch
* XXX1
本地在XXX1，则会默认push/pull远程的XXX1