---
title: CentOS后上a使在CentOS上使用Python3，出现'ascii'编码问题
date: 2021-05-25 10:36:36
tags: [Python,CentOS]
categories: Linux
---

## 情况
> 本地 Windows10: python3.8.5
>      MAC OS: python3.9
> CentOS : python3.9


> 将项目上传到服务器后，运行出现 
> `UnicodeEncodeError: ‘ascii’ codec can’t encode characters in ordinal not in range(128)`
> 经过一番排查，发现是Linux的坑，因为我在 Ipython 中 print(中文) 一样报这个错误，最后经过搜索，改Linux服务器的编码就好了

```python
# 查看当前编码
import sys
sys.stdout.encoding

# 你的输出结果可能和我不同，但只要不是utf-8，就都有可能出现以上问题
'US-ASCII'
```
## 解决
```shell
# 在 `~/.bash_profile` 中添加以下一行代码，我这里使用vim

vim ~/.bash_profile

# 添加  
`export LANG="en_US.UTF-8"`

# 保存退出后，更新配置使，更改生效
source ~/.bash_profile
```

> 可以用 python交互 再次查看编码，发现是 UTF-8 就可以了