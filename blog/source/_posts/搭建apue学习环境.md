---
title: 搭建apue学习环境
date: 2019-09-26 12:24:29
tags: Linux
categories: 环境搭建
---

<!--more-->

## CentOS7
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



## Ubuntu

#### 1.下载apue源码
[apue](http://www.apuebook.com/code3e.html)

#### 2. 解压
```shell
tar -zxvf src.3e.tar.gz
```
#### 3. 安装编译所需要的中间件
```
sudo apt-get install libbsd-dev
```
#### 4. 执行make编译
```
make
```

#### 5. 复制到/usr/include

```
cd ./apue.3e

sudo cp ./include/apue.h /usr/local/include/
sudo cp ./lib/libapue.a /usr/local/lib/
```

#### 6. 修改/usr/include/apue.h
```shell
cd /usr/include
sudo vim apue.h
#在#endif /* _APUE_H */上一行插入
#include "error.c"
```
![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20190926122802.png)

以上apue的环境就搭建完成了