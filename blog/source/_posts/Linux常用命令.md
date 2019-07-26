---
title: Linux常用命令
date: 2019-07-14 15:06:55
tags: [Linux]
categories: 常用操作
---

前言:

> 上个星期利用Hexo搭建了博客,只是为了记录学习过程踩到的坑和总结知识点,以备后续复习使用。先在这里立个FLAG,以后每个星期至少要更一篇博文!
> 今天我们来谈谈Linux的命令,当然是我自己这段时间从入门Linux用到较多的命令。
> grep sed awk作为三剑客,会单独作为一篇博文絮叨。
> 本次命令操作环境主要是在CentOS7.0。

<!--more-->

# 1. 文件与目录
```shell
ls filename   			
#常用的选项与参数
-d		   			
-a         				#列出所有文件,包括隐藏文件
-l         				#详细信息显示, 另外ll是 ls -l 的别名

cd filename        		

pwd
#显示当前目录路径
-P		  				#显示绝对路径

mkdir dirname     		 
-p         				
#递归的创建,若上一级目录未创建则会创建上一级目录

rmdir      	
-p						
#递归的删除空目录,当空的子目录被删除后为空的目录,也会被删除

touch filename      	

cp sourcefile destinationfile  
-i						#若目标文件已存在,则在覆盖时会进行询问
-p						#连同文件的属性一起复制(备份常用)
-r						#递归的复制

cd ~
#返回家目录
cd ..					
#返回上一级目录

rm filename				
-r						#递归删除
-f						#force,忽略不存在的文件,不出现警告
-i						#删除前进行询问

mv sourcefile destinationfile	 #移动文件
#在同一个目录下进行移动可以进行重命名,如下:
mv test test.txt		#将test文件名改为test.txt

cat	filename
#concatenate,并不是猫的意思
-n						
#显示行号,与-b不会显示空行行号,-n全部都会显示

tac filename        	
#反向显示,感觉有点像彩蛋的意思..
more filename			
#与cat不同,more不是一次性全部显示
#操作
#空格键:代表下一页
#Enter:代表向下一行
#q:退出

less
#和more一样一页一页的翻动
#PgUp:向上翻页
#PgDn:向下翻页
#/字符串:想下查找字符串
#n:查找下一个字符串
#N:查找上一个字符串
#q:退出

head
#读取前几行
-n				  		#指定行数,例如 head -n 10 test

tail
#读取后几行
-n				 		#指定行数

tree             		#查看目录树

diff filename1 filename2 
#用于比对两个文件之间的差异,并且是以行为单位来比对
file filename             
#用来识别文件类型,通过查看文件的头部信息来获取文件类型

test          			#检查某个条件是否成立,可用于数值、字符和文件三个方面的测试
-e            			#判断文件名是否存在
-f            			#判断文件名是否存在且为文件
-d            			#判断文件名是否存在且为目录
#shell script中常用[]来代替test作为判断符号,需注意的是中括号两端必须要有空格

文件名搜索
which    [command]        #根据PATH环境便令去寻找执行文件
which -a [command]        #把PATH目录可找到的指令均列出来

whereis [-lbs] filenam    #主要针对/bin/sbin目录下的执行文件和/usr/share/man底下的manpage文件
-l  列出whereis查询的目录
-b  只找二进制文件
-s  只找源文件

locate/updatedb             #安装locate   yum install mlocate
locate [-iS] filename       #查找的是/var/lib/mlocate
-i 忽略大小写
-S 输出locate所使用的数据库文件的相关信息
#updatedb 更新数据库文件信息

find [PATH] [-ioname / -type / -user] [字符串]

//type                      #一般用于判断一个命令是否为内置命令
```

# 2.远程操作以及查看系统进程与端口号
```shell
远程拷贝scp
scp root@ip:/source /destination    #远程到本地
scp /source root@ip:/destination    #从本地到远程
-r   				#递归的传文件
 
远程登陆
ssh root@ip

远程免密登陆
ssh-keygen -t rsa          # ssh-keygen生成RSA密钥和公钥 -t表示type
#会生成~/.ssh/id_rsa和~/ssh/id_rsa.pub,私钥和公钥
ssh-copy-id username@remote-server 
#将ssh公钥上传到远程服务器上
 
ps            #用于列出系统当前运行的那些进程
ps -ef        #显式所有进程信息,连同命令行,一般配合grep
ps aux        #列出当前内存正在运行的程序,配合grep
ps -l         #列出当前属于自己本次登入的PID与相关信息列出来

安装lsof	#	yum -y install lsof
查看端口号:80
lsof -i : 80

或者使用netstat
netstat -anp | grep 80
```

# 3.磁盘状态的查看以及解压缩
```shell
du              #评估文件系统的磁盘使用量
du -ha
du -h  --max-depth=1

df -hl         	#以人类易理解的方式列出系统的整体磁盘使用量

lsblk           #列出系统上的所有磁盘列表
fdisk -l        #显式磁盘的详细信息


.xz 
xz -zdtk  
#xz默认带有 -z 压缩,另外xz拥有很好的压缩比
-d 解压缩
-t 测试压缩文件的完整性
-k 保留原本文件不删除
xz filename   #==> 默认带 -z 压缩filename文件

tar -zjJxvfcC
-z 透过gzip进行压缩/解压,后缀名 *.tar.gz
-j 透过bzip2进行压缩/解压,后缀名为 *.tar.bz2
-J 透过xz进行压缩/解压,后缀名为 *tar.xz  
#zjJ不能同时出现
-x 解压缩
-v 解压过程中将正在处理的文件名显示出来
-f filename  -f 后面要接被处理的文件名,一般把f放最后
-c 打包
-C 指定解压缩的目录
```

# 4.身份切换以及别名
```shell
su                  #单纯使用,切换到root身份
su - username       #切换到username用户 - 和用户名之间有空格

sudo                #以root权限的身份执行命令,并且只需要用户密码
#但并非所有人都可以使用sudo命令,只有在/etc/sudoers内的用户才能使用

chown               #将指定文件的拥有这改为指定用户或者组
-R 处理指定目录以及其子目录下的所有文件
-v 显示详细的处理信息
chown -R -v root:root test.txt #将test.txt文件改为root:root

chmod               #控制用户对文件的权限的命令
# r=4 w=2 x=1
-R 处理指定目录以及其子目录下的所有文件
-v 显示详细的处理信息
chmod 777 test.txt


alias                     #别名
alias g++='g++ -std=c++11'
#在~/.bashrc 里加入才会一直生效
#改环境变量要到 /etc/profile中改才会一直生效
#/etc/profile 是系统整体的设定,帮所有使用者设定整体环境
```
# 5.通配符、正则表达式和shell中数组
```shell
Bash中的通配符
*         #表示0个到无穷多个任意字符
?         #代表一定有一个任意字符
[]        #一定有一个在括号内的字符。例如:[abcd]代表一定有一个字符,可能是a,b,c,d中的任意一个
[-]       #若有减号在中括号内,代表在编码顺序内的所有字符。例如:[0-9]代表0到9之间的所有数字,因为数字的语系编码是连续的。
[^]       #若有指数符号(^),则代表反向选择,例如:[^abc]代表一定有一个字符,不包括abc

#通配符代表的是bash操作接口的一个功能,正则表达式是一种字符串处理的表示方式!

正则表达式
^word    #待搜索的字符串在行首
word$    #待搜索的字符串在行位
.        #代表一个有意义的字符,与Bash通配符中的?作用相似
\        #转意字符
*        #易与Bash通配符中的*混,这里的*表示重复0个到无穷多个*前面的字符
[]       #代表一个待搜索的字符,例如:a[afl]y,表示aay,afy,aly都可以
[n1-n2]  #代表想要截取的字符的范围,-有特俗意义代表两个字符之间所有连续的字符
         #如:[A-Z]表示所有大写字字符
[^list]  #不要列出字符串或范围
\{n,m\}  #连续的n到m个前一个字符
         #例如:'go\{2,3\}g' 在g与g之间有2个到3个的o存在的字符串 
         
         
数组
shell数组是用括号来表示,并且元素之间用"空格"分割开来
例如:下面就是一个传入文件,并且遍历文件名修改所属组的过程
    #!/bin/bash
    #shell中改变文件夹下的所属用户及分组
    array_filename=($@)
    echo "" >log.log
    for ix in ${array_filename[@]}
    do
        chown -R -v root:root ${ix} >>log.log
   done
```