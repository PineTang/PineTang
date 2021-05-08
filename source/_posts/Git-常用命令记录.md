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

## git pull 相关
### 放弃本地修改，强制拉取远程更新
1. stash 暂存（推荐）
> git stash可以将代码暂时保存，不会因为本地修改，而影响对远程分支的代码pull/push，又可以保留现在的项目进度
```git
首先，将所有代码添加至暂存区：
git add .

然后，将代码临时保存：
git stash

此时代码会重置到修改前的状态，可以同步远程仓库区了。
git pull

同步后，如果还想继续修改原来的代码，可将临时代码恢复至工作区：
git stash pop
```
更多用法可以参考![git stash](https://www.cnblogs.com/zndxall/archive/2018/09/04/9586088.html)

2.reset 回退
> reset 比较暴力，可适用于 代码在工作区、暂存区、仓库区等任何场景，重置后不可恢复，需要确认，你完全放弃当前的修改。
```git
git fetch --all
git reset --hard origin/master
git pull
```
`git fetch 指令是下载远程仓库最新内容，不做合并。`
`git reset 指令把HEAD指向master最新版本。`
> 更多用法
reset --hard：重置后不保留暂存区和工作区
reset --soft：保留工作区，并把重置 HEAD 所带来的新的差异放进暂存区（此时代码的变更状态相当于执行完 git add命令）
reset --mixed：reset的默认参数，保留工作目录，并重置暂存区（此时代码的变更状态相当于执行 git add命令之前）