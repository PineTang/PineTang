---
title: 用hexo搭建博客，并配置主题
date: 2021-04-12 18:03:07
tags: [hexo,themes]
categories: 个人
---

## 安装

> 安装以下内容所需环境：npm，git
 1. hexo
 2. hexo-theme-argon
 
1. [安装hexo文档][1]
> hexo 安装后进入你安装的目录/themes下，下载主题

2. 下载主题，[主题地址][2]
    ```shell
    # 在 你的目录/themes 下进行clone
        1. git clone https://github.com/solstice23/hexo-theme-argon.git
        2. 将下载的主题更名为：argon
    
    ```
    
3. 配置主题相关修改
    ```shell
    # 在 Hexo 你的目录/_config.yml 中将 theme 项改为 argon
    # 将 Hexo 你的目录/themes/argon/_config.yml 复制到 ，你的目录/source/_data（没有则自建） 文件夹中，并重命名为 argon.yml
    # 在 你的目录/themes 下操作
        1. npm install hexo-generator-search --save
    # 在 Hexo 你的目录/_config.yml 下操作
        注意缩进
        1. search:
              path: search.xml
              field: post
              content: true
    ```

4. 启动即可预览
    ```shell
    # 在 你的目录/ 下操作
    hexo server
    ```


  [1]: https://hexo.bootcss.com/docs/index.html
  [2]: https://github.com/solstice23/hexo-theme-argon