---
title: 'JavaVM FATAL: Failed to load the jvm library.'
date: 2022-03-01 18:23:14
tags: [环境配置, Android]
categories: Mac
---

## 错误情况
```shell

: monitor 
JavaVM: Failed to load JVM: /Users/me/Library/Java/JavaVirtualMachines/corretto-1.8.0_312/Contents/Home/lib/libserver.dylib
JavaVM FATAL: Failed to load the jvm library.
```

## 解决
```shell

cd /Users/me/Library/Java/JavaVirtualMachines/corretto-1.8.0_312/Contents/Home/lib/
sudo ln -s ../jre/lib/server/libjvm.dylib libserver.dylib
```