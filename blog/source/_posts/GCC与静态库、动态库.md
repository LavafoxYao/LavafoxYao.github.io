---
title: GCC与静态库、动态库
date: 2019-10-23 11:28:20
tags: Linux
categories: 操作系统
---

#### GCC 常用指令
```shell
man gcc
```
gcc工作流程
例如: gcc hello.c
```
//***第一步***
gcc -E hello.c >hello.i   
//-E(预处理),头文件展开,宏替换
//默认打印出来不保存,重定向到hello.i的文件里 

//***第二步***
 gcc -S hello.i   //生成汇编代码,生成hello.s
 
//***第三步***
gcc -c hello.s    //将汇编编译成二进制文件,生成
hello.o

//***第四步***
gcc hello.o       //链接 生成 a.out文件
```
-I 包含(相对路劲或绝对)路径下的头文件(或目录,下面指的是./include/ 目录)
```
gcc hello.c -I ./include/
```

指定目标(不是生成a.out)
```
gcc hello.c -I /include/ -o hello
```

-g --用于gdb调试,不加此选项不能gdb调试
```
gcc hello.c -I ./include/ -o hello -g
```
-Wall --显示更多的警告
```
gcc hello.c -I ./include/ -o hello -g -Wall
```
-lstdc++ 用C++的方式去编译
```
gcc hello.cpp -lstdc++ -o hello
```
-O 优化选项,1-3越高优先级越高
```
gcc hello.c -I ./include/ -o hello -g -Wall -O1
gcc hello.c -I ./include/ -o hello -g -Wall -O2
gcc hello.c -I ./include/ -o hello -g -Wall -O3
```
gcc参数小结:

-I 包含头文件路径

-L 包含库文件的路径

-l 库名 libxxx.so -lxxx

-O 优化选项,1-3级

-W 警告all显示更多

-g 用于gdb调试

#### 静态库
**命名规则** lib + 库的名字 + .a 例: libmytest.a

制作步骤
> 1. 编译成.o文件   (gcc -c *.c)
> 2. 将.o文件打包: ar rcs libmyrepo.a  file1.o file2.o
> 3. 将头文件与库一起发布(一般而言移动到/include/ 下)

使用静态库
```
gcc  main.c -o main.app -I include/ -L lib/ -l mytest 

//-l 后面的名字掐头去尾,如libmytest.a ==> -l mytest
```
优点:执行块,发布应用时不需要发布库
缺点:执行程序体积会比较大,库变更时需要重新编译应用

#### 动态库
使用的时候临时加载进来

制作动态库
> 1. gcc -fPIC xxxx.c  编译与位置无关的代码
> 2. 将.o文件打包,关键参数 -shared

```shell
gcc -fPIC -c *.c -I ../include/
gcc -shared -o libmytest.so *.o  //-o 指定目标libmytest.so
```

动态链接库的使用
> 将 libmytest.so 软连接到 /lib 下 (不推荐)
> 将库路径增加到环境变量LD_LIBRARY_PATH中
> 配置/etc/ld.so.conf文件 添加需要添加的 .so 文件的路径,执行 sudo ldconfig -v

优点: 执行程序体积小,库变更时一般不需要重新编译应用
缺点: 执行时需要加载动态库,相比静态库而言更慢;发布应用时需要同时发布动态库