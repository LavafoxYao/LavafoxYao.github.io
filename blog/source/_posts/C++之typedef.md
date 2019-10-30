---
title: C++之typedef
date: 2019-10-30 18:47:40
tags: C++
categories: C++

---

在看APUE的时候发现typedef有些声明让我感觉很陌生,就在网上找相关blog看了下,也写了点如下的笔记.

<!--more-->

### define 

define 是宏,是在预处理阶段用宏来替换字符串.
#define 与编译器无关,单纯进行无脑替换.

```c++
#define mian main 
// 用 mian 替换成字符串 main
int mian(int argc, char *argv[])
{
    return 0;
}
```

### typedef 

typedef属于编译的一部分,可以定义中类型的别名,而不是简单的宏替换,能够起到代码整洁的作用.

- 数组

```c++
typedef int A[];  // 定义一个名为A的ints数组的类型
typedef char String[32]; //String 代表具有32个char元素的char型数组
```

主要说明下typedef在处理复杂类型时候的用法.

- 函数

```c++
typedef int f();//定义一个名为f, 参数为空,返回值为int的函数类型
typedef int g(int);//定义一个名为g, 含一个int参数, 返回值为int行的函数类型
```

```c++
void (*signal(int signo,void (*func)(int)))(int)
/*
 *signal函数原型说明此函数要求两个参数,返回一个函数指针
 *而该指针所指向的函数无返回值(void)
 *第一个参数signo是一个整型数,第二个参数是函数指针
 *signal的返回值是一个函数地址,该函数有一个整形参数(即最后的(int))
*/

//上面的函数定义实在太复杂了
typedef void Sigfunc(int);
//Sigfunc变成了一个复合类型
Sigfunc *signal(int signo,Sigfunc *);
```

##### typedef与const组合使用

```c++
typdef char * pStr;
char string[12] = "hello,world";
const char *p1 = string;
const pStr p2 = string;
p1++;
p2++;               //error
```
typedef的作用是:任何声明变量的语句前面加typedef之后,原来是变量的都变成了一种类型.

const pStr ==> const (char *)  这里就类似于 const int i = 1;

这里的const 直接修饰 i ,所以不能对 i进行修改, 由于p2 **不是指针** 也是如此,const修饰的是p2.