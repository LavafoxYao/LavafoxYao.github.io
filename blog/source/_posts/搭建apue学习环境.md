---
title: 搭建apue学习环境
date: 2019-09-26 12:24:29
tags: Linux
categories: 环境搭建
---

看了很多`blog`踩了很多坑，在此总结下。

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

##  使用err_sys()等函数报未定义错误解决办法

`err_sys`和`err_quit`等函数并不是系统调用和库函数，而是作者自己编写的函数，需要自建一个`.h`文件将下面的代码补充上，并在每次使用上述函数的时候包含该文件。
例如：将改文件命名为`apueerror.h` ，将该头文件放在和`apue.h`同一个目录下（我的目录是/usr/include 。

每次使用apue中的函数的时候最好都包含进来

```C
#include "apue.h"
#include "apueerror.h"
```



#### `apueerror.h`包含的代码如下: 

```C
#include <errno.h> /* for definition of errno */
#include <stdarg.h> /* ISO C variable aruments */

static void err_doit(int, int, const char *, va_list);

/*
 * Nonfatal error related to a system call.
 * Print a message and return.
 */
void
err_ret(const char *fmt, ...)
{
    va_list ap;

    va_start(ap, fmt);
    err_doit(1, errno, fmt, ap);
    va_end(ap);
}


/*
 * Fatal error related to a system call.
 * Print a message and terminate.
 */
void
err_sys(const char *fmt, ...)
{
    va_list ap;

    va_start(ap, fmt);
    err_doit(1, errno, fmt, ap);
    va_end(ap);
    exit(1);
}


/*
 * Fatal error unrelated to a system call.
 * Error code passed as explict parameter.
 * Print a message and terminate.
 */
void
err_exit(int error, const char *fmt, ...)
{
    va_list ap;

    va_start(ap, fmt);
    err_doit(1, error, fmt, ap);
    va_end(ap);
    exit(1);
}


/*
 * Fatal error related to a system call.
 * Print a message, dump core, and terminate.
 */
void
err_dump(const char *fmt, ...)
{
    va_list ap;

    va_start(ap, fmt);
    err_doit(1, errno, fmt, ap);
    va_end(ap);
    abort(); /* dump core and terminate */
    exit(1); /* shouldn't get here */
}


/*
 * Nonfatal error unrelated to a system call.
 * Print a message and return.
 */
void
err_msg(const char *fmt, ...)
{
    va_list ap;

    va_start(ap, fmt);
    err_doit(0, 0, fmt, ap);
    va_end(ap);
}


/*
 * Fatal error unrelated to a system call.
 * Print a message and terminate.
 */
void
err_quit(const char *fmt, ...)
{
    va_list ap;

    va_start(ap, fmt);
    err_doit(0, 0, fmt, ap);
    va_end(ap);
    exit(1);
}


/*
 * Print a message and return to caller.
 * Caller specifies "errnoflag".
 */
static void
err_doit(int errnoflag, int error, const char *fmt, va_list ap)
{
    char buf[MAXLINE];
   vsnprintf(buf, MAXLINE, fmt, ap);
   if (errnoflag)
       snprintf(buf+strlen(buf), MAXLINE-strlen(buf), ": %s",
         strerror(error));
   strcat(buf, "\n");
   fflush(stdout); /* in case stdout and stderr are the same */
   fputs(buf, stderr);
   fflush(NULL); /* flushes all stdio output streams */
}
```

