---
title: 将Vim里的内容粘贴到Vim之外
date: 2019-10-25 11:09:26
tags: Vim
categories: 生产工具及环境的折腾
---

**实验环境为Centos 7**

Vim默认是无法和"外界"进行交流的.

首先查看Vim的clipboard功能是否开启
```
vim --version | grep clipboard
```
![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/VIM%E5%A4%8D%E5%88%B6%E7%B2%98%E8%B4%B41.png)

**clipboard** 前面有个 "-" 说明此功能未开启

**CentOS**

安装vim-x11和vim-enhanced
```
yum install -y vim*
```
![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/VIM%E5%A4%8D%E5%88%B6%E7%B2%98%E8%B4%B42.png)

```
vim /etc/bashrc

//进入bashrc文件中,在末尾添加下面的alias命令

alias vim=/usr/bin/vimx
```
再次试验vim
![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/VIM%E5%A4%8D%E5%88%B6%E7%B2%98%E8%B4%B43.png)

就可以愉快的进行Vim与外界的复制粘贴了.