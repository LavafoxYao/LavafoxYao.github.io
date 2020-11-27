---
title: C++之内存布局
date: 2020-11-27 15:50:56
tags: C++
categories: C++
---

很久之前看过<深入探索C++对象模型>大概也只能记得个虚表机制，但是最近在看虚继承的时候发现自己还是有点纠结C++内存布局，今天我就来实验并总结下。

<!--more-->

# C++之内存布局

### 前言

`C++`作为`C语言`的超集， 在无虚函数的情况下，C++并没有额外增加内存消耗。为了实现多态这一面向对象的性质，`C++`仅仅在每个含有虚函数的类中增加了一个虚函数指针。



首先来看看我们常说的C++虚拟内存的分布。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/stack_heap.png)

根据上图我想到两个常见的问题：

1. **为什么栈的效率要比堆的效率高很多?**

   - 栈是系统提供的数据结构，计算机底层对栈提供支持：分配**专门的寄存器**（一般而言是一级存储结器），压栈和出栈都有专门的指令（好家伙玩特权！）。堆则是由C/C++函数提供(堆一般存放在二级存储器)，库函数按照一定的算法在堆内存中搜索可用的空间大小。这样显然效率会比栈要低得多。

     

2. **为什么栈要向下增长而堆要向上增长？**

   - **这样设计可以使得堆和栈能得到充分利用空闲的地址空间。**如果栈向上增长的话那么就需要指定栈和堆的一个严格分界线。但是这个分界线没有很好的划分标准。有的程序使用的堆空间较多，有的程序使用的栈空间多。**最好的方法就是让栈和堆一个向上增长，一个向下增长，这样它们就可以最大程度共用这块剩余的地址空间，达到利用率的最大化。**

# C++多态机制的本质

当调用对象的成员函数，编译器是如何识别是哪个对象在调用呢？



**答案：编译器在编译阶段，进行函数的重构，即将成员函数进行非成员化。通过将this指针作为函数的第一个参数,通过this指针即可以找到对象的数据成员;**



例如：

```C++
Class Example {
public :
   int a;
   Example,a(a)(int a);
   void print();
}
// ================编译前=============
int main(){
    Example e = new Example(10);
    e.print();
    return 0;
}

// ==============编译后=============
int main(){

    Example e = new Example(10);
    print(&e);
    return 0;
}
```

### 走进虚函数的世界

为了证实我们说的理论有具体的数据支撑，下面我们所有的测试都是在VS2019下进行的。



让我们先看看如何在vs2019上查看函数的布局。

1. 右键vs2019上`xxx.cpp`文件选中属性。
   ![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/Snipaste_2020-11-27_11-10-09.png)
2. 先选择左侧的C/C++->命令行，然后在其他选项这里写上`/d1 reportAllClassLayout`，它可以看到所有相关类的内存布局，如果写上`/d1 reportSingleClassLayoutXXX（XXX为类名）`，则只会打出指定类XXX的内存布局。
   ![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/Snipaste_2020-11-27_11-11-30.png)

#### 无继承状态

```C++
class Base {
	int a;
	int b;
	virtual void func();
};
```

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/Snipaste_2020-11-27_11-17-42.png)
从上图可以看出，这个内存结构图分成了两部分，上面是内存分布，下面是虚表。

上半部： `vs2019`把虚表指针放在了内存开始处(0地址偏移量)，然后再是依次声明的成员变量。

下半部： 紧跟在`&Base_meta`后面的0表示，这张虚表对应的虚指针在内存的分布。下面列出了虚函数，左侧的0是这个虚函数的序号，很显然这里只有一个虚函数。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/vptr.png)

#### 单继承状态

```C++
class Base {
public:
	int a;
	int b;
	virtual void func_1();
};

class Derive: public Base {
public:
	int c;
	virtual void func_2();
};
```

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/Snipaste_2020-11-27_14-22-02.png)

上图看来只是多了一个继承的类，从`&Derive_meta`后面可以看到有两个虚函数。

另外子类的内存布局:

1. 虚函数指针
2. 基类的成员函数
3. 子类的数据

具体的内存布局图如下所示:

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/vptr2.png)

#### 多继承状态

```C++
class Base1 {
public:
	int a;
	int b;
	virtual void func_1();
};

class Base2 {
public:
	int c;
	virtual void func_2();
};

class Derive: public Base1, Base2 {
public:
	int d;
	virtual void func_3();
};
```

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/Snipaste_2020-11-27_14-41-42.png)

从上图我们可以看出`Derive`的内存布局为: 首先是从左往右的继承.

```
1. 从Base1那里得到的虚函数指针(它继承来的虚函数指针和Base1中的指针并不是同一根)
2. Base1的成员变量
3. 从Base2那里得到的虚函数指针(它继承来的虚函数指针和Base2中的指针并不是同一根)
4. Base2的成员变量
5. 自身成员变量
```

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/v_ptr3.png)

##### C++中的多态是怎么实现的呢?

谈到多态的实现首先要知道两个机制 1. 静态类型与动态类型 2. 虚函数表

1. 静态类型与动态类型
   - 静态类型:变量(对象)自身的类型 ===> 编译时确定
   - 动态类型:指针(引用)所指向对象的类型 ===> 运行时确定
2. 虚函数表就是我们上面讨论的.

实现C++多态，也就是动态绑定的过程大致分为三步: 

1. 调用对象必须是指针或引用 
2. 调用的函数为虚函数  
3. 类型转换方向是向上转换



####  虚继承

虚继承是多重继承中特有的感念。虚基类是为了解决多重继承中冗余成员而出现的。如类C继承自B1，B2，而类B1，B2都继承类A，因此类C中会出现两次类A中的变量和函数。**为了节省内存，可以将B1，B2对A的继承定义为虚继承，而A就成为虚基类。**

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/v_hire.png)



```C++
class Base {
public:
	int a;
	int b;
	virtual void func_1();
};

class Derive: virtual Base {
public:
	int c;
	virtual void func_2();
};
```

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/Snipaste_2020-11-27_15-14-27.png)

注意看这张图，`Derive`的内存布局一开始是一个`vfptr`是一个虚函数指针(自己的)，紧跟着一个`vbptr`(虚基类指针)，-4表示偏移量。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/v_ptr4.png)

**虚基类表指针（vbptr）**，该指针指向了一个虚基类表,虚表中记录了虚基类与本类的偏移地址。通过偏移地址，这样就可以找到虚基类成员，而虚继承也不用像普通多继承那样维持着公共基类（虚基类）的两份同样的拷贝，从而节省了存储空间。

