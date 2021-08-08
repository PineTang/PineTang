---
title: Mac-环境配置-ADB
date: 2021-08-09 00:29:21
tags: [环境配置, ADB]
categories: Mac
---

> 背景： 我这里因为已经下载好了 Android Studio 所以 不用去单独下载 adb 了，只需要配置一下文件即可
如果你也安装了 Android Studio 那么可以在这个路径下找到 adb 脚本
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/202108090035_1.png)
> 拷贝路径后

```shell
# 修改文件，没有的话会创建
vim ~/.bash_profile
```
```shell
export ANDROID_HOME="/Users/用户名写自己的/Library/Android/sdk"
export PATH=${PATH}:${ANDROID_HOME}/tools
export PATH=${PATH}:${ANDROID_HOME}/platform-tools

# 如果是单独下载的 adb 工具，直接只添加下边这一行应该也是可以的 
#（确保你填写的文件夹路径下有 adb 脚本）
export PATH=${PATH}:你的adb文件夹路径

```
> 然后执行
```shell
source ~/.bash_profile

adb --version
# 有以下输出就代表OK了

Android Debug Bridge version 1.0.41
Version 31.0.3-7562133
Installed as /Users/xxxxxx/Library/Android/sdk/platform-tools/adb
```
