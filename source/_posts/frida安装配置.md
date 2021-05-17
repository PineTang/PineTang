---
title: frida安装配置
date: 2021-05-17 16:15:36
tags: [frida安装, Android逆向]
categories: frida
---

> 总共需要
* 1. windows frida安装
* 2. 手机frida-server安装

## windows frida安装
> 需要具备python环境，有pip
### 安装frida
```shell
pip install frida
pip install frida-tools 
```
> pip list 查看安装情况
```shell
pip list

frida                  14.2.18
frida-tools            9.2.4
```

## 安卓手机 安装frida-server
### 安装
> 根据pip 安装的frida 版本 前往github下载对应server版本

[下载][frida-github]
> 打开下载页面后，往下翻找到 frida版本下 frida-server 找到安卓版
![img](https://gitee.com/PineKer/myfiles/raw/master/blog/frida-releases.png)

> * CPU架构和位数：
armeabi-v7a -------（32位ARM设备）
arm64-v8a -------（64位ARM设备）
> * 查看手机cpu型号
```shell
// 数据线连接忽略此操作
PS C:\Users\alldemensions> adb connect 192.168.6.211:5666
connected to 192.168.6.211:5666

// 查看cpu信息
PS C:\Users\alldemensions> adb shell getprop ro.product.cpu.abi
armeabi-v7a
```
> 所以我这里下载frida-server-14.2.18-android-arm.xz版本就可以了
下载后解压得到文件，将文件push到手机设备上
```shell
adb push 解压后文件名 /data/local/tmp

// 进入手机
adb shell   (多个设备的话： adb -s 设备名 shell  ；设备名可以adb devices查看)

// root权限
su

// 进入push到的文件目录
cd data/local/tmp

// 修改文件执行权限
chmod 777 解压后文件名

//查看权限
ls -al
-rwxrwxrwx root     root     17673940 2021-05-17 16:08 fds14.2.18-ad-arm
```
### 启动
```shell
// 端口转发 (注意：这里在Windows终端中执行)
adb forward tcp:27042 tcp:27042
adb forward tcp:27043 tcp:27043

// 在手机中(adb shell操作后)启动server
root@generic:/data/local/tmp # pwd
/data/local/tmp

root@generic:/data/local/tmp # ./fds14.2.18-ad-arm

```
> 如果堵塞在这里，没有错误输出，就是启动成功了

### 验证
> 回到Windows终端中
```shell
PS C:\Users\alldemensions> frida-ps -U
  PID  Name
-----  ------------------------------
23390  adbd
23901  android.process.acore
24085  android.process.media
26492  com.alimama.moon
26622  com.alimama.moon:pushservice
24064  com.android.dialer
24173  com.android.inputmethod.latin
23808  com.android.launcher3
23738  com.android.phone
24114  com.android.providers.calendar
...
```
> 至此，全部安装已经完成，可以进行逆向分析调试了


[frida-github]:https://github.com/frida/frida/releases