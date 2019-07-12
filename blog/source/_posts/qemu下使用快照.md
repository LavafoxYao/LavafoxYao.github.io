---
layout: '[layout]'
title: qemu下使用快照
date: 2019-07-06 16:36:26
tags: 
- 生产工具及环境的折腾
categories: 生产工具及环境的折腾
---

前言:

> 最近实习工作的主要内容是在转移我们组的代码,以实现产品的国产化。在代码转移的过程中与我们合作的其他部门的童鞋在转移so文件的时候一不小心替换了某些重要目录下的so,导致虚拟机上的环境不能正常运行。为了防止这类事故再次发生,我的师傅让我去学习下如何在qemu上打快照,如下的两种方法是我已经尝试成功的方法,当然不全。本次实验环境为Centos7.0。
>
> <!--more-->

## 1.使用qcow2格式下的snapshot

```shell
	qemu-img info cneos_ips_lihuiyun.disk     #查看格式
```

![使用的镜像格式](<https://raw.githubusercontent.com/LavafoxYao/MarkdownPhotos/master/1.png>)

由于格式是raw需要转换成qcow2格式

```shell
	qemu-img convert -f raw  cneos_ips_lihuiyun.disk -O  qcow2 cneos_ips_lihuiyun.qcow2
```

![](<https://raw.githubusercontent.com/LavafoxYao/MarkdownPhotos/master/2.png>)

注意经过测试使用snapshot打印快照只保存关机时刻的虚拟机状态,意思就是如果你在开机状态使用snapshot打印快照并不会保存运行时的状态,而是保存本次关机之后的状态,所以snapshot建议关闭qemu再使用。

```shell
	qemu-img snapshot -c snapshot01 cneos_ips_lihuiyun.qcow2 #创建snapshot01
	qemu-img snapshot -l snapshot cneos_ips_lihuiyun.qcow2   #查看快照
```

![](<https://raw.githubusercontent.com/LavafoxYao/MarkdownPhotos/master/2.png>)

```shell
	qemu-img snapshot -d snapshot02 cneos_ips_lihuiyun.qcow2  #删除snapshot02
	qemu-img snapshot -a snapshot01 cneos_ips_lihuiyun.qcow2  #使用snapshot01
```

开启虚拟机就可以使用snapshot01快照了。
	
注意这个快照是内置快照,将快照数据和磁盘数据放在一个qcow2文件中。

## 2.使用拷贝来实现快照

```shell
     cp ~/workspace/cneos_ips_lihuiyun.disk ~/workspace/backup/cneos_ips_lihuiyun.disk   #拷贝整个系统
```

直接使用~/workspace/backup/cneos_ips_lihuiyun.disk 的镜像就可以实现快照,经过测试这个可以动态的保存文件,也就是说在你cp的那个时间节点的数据全部都会保存下来,而不像上一种方法需要关机才能保存当前的状态。缺点也显而易见,是浪费了磁盘空间。