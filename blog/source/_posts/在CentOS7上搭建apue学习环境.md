---
title: 在CentOS7上搭建apue学习环境
date: 2019-09-26 12:24:29
tags: Linux
categories: 环境搭建
---

<!--more-->

#### 1.下载apue源码

[apue](http://www.apuebook.com/code3e.html)

#### 2. 解压
```shell
tar -zxvf src.3e.tar.gz
```

#### 3. 复制到/usr/include
```
cd ./apue.3e

# root权限的情况下
cp ./include/apue.h /usr/include
cp ./lib/error.c /usr/include
```
#### 4. 修改/usr/include/apue.h
```shell
cd /usr/include
vim apue.h
#在#endif /* _APUE_H */上一行插入
#include "error.c"
```

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20190926122802.png)

以上apue的环境就搭建完成了