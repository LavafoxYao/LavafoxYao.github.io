---
title: C++之右值与完美转发
date: 2020-08-20 20:03:54
tags: C++
categories: C++
---

最近想把`C++`中容易忘记和一些细节部分做个笔记，于是就有了今天的`blog`

<!--more-->

#### 什么是右值？

早期的`C语言`给出的定义是：左值是一个表达式，可以出现在赋值操作的左边或右边，而右值却只能出现在赋值操作的右边。

```C++
int a = 3;				//左值
double b = 3.14;		//左值
a * b  = 10;			//Error 因为 a * b是一个右值, 右值只能出现在赋值操作的右边
```

根据`<<C++ Prime>>`  中对左值和右值的归纳：

- 当一个对象被用作右值的时候，用的是对象的值（内容）
- 当对象被用作左值的时候，用的是对象的身份（在内存中的位置）；

简而言之就是：左值可以被解释成右值，右值不可以解释为左值。**主要原因是左值在内存中有一份属于自己的内存空间，而右值只是一个临时对象。**

标准库也提供了`move()`把一个左值变成一个右值。但我们要明确知道一点当我们调用`move(a)`后不再使用`a`。

- move()的调用是告诉编译器：我们有一个左值，但是我们希望像一个右值一样去处理。

#### 左值与右值的区别

左值有持久的状态，而右值要么是字面常量，要么是在表达式求值过程中创建的临时对象（函数返回值也是右值）。

#### 右值引用

```C++
int i = 42;
int &r = i; 				//左值引用
int &&rr = i;				//Error 不能将一个右值引用绑定到一个左值上
int &r2 = i * 10;			//Error i * 10是一个右值
const int &r3 = i * 10;		//可以将一个const的引用绑定到一个右值上
int &&rr2 = i * 10;			//将一个右值绑定到一个右值引用上
```

由于右值引用只能绑定到临时对象上，我们可知：

- 所引用的对象将要被销毁
- 该对象没有其他用户

**以上两条性质：我们可以从绑定到右值引用的对象`steal`状态。这样也就解决了非必要的拷贝。**



#### 移动构造函数

我们知道`C++`在四种情况下会调用**拷贝构造函数**

1. 初始化一个对象
3. 函数参数的形实结合
3. 函数栈区的对象会复制一份到函数的返回
```C++
// 关于第3点
class A{
//
};
A f(){
    A a;
	return a; //先调用copy构造函数，用a对象创建了一个匿名对象；再执行a的析构函数（因为a为局部对象）
}

int main(){
    A a = f();	//匿名对象直接去初始化a，不会调用copy构造函数, 调用完毕匿名对象执行析构函数
}
```

拷贝构造函数会占用大量的系统资源（分配内存 释放内存）。从而降低了程序的执行效率，于是`C++11`提出了移动构造函数这一概念。

参考`侯捷老师在GeekBand`的讲解

```C++
class Mystring{
private:
	char *_data;
    //...
public:
    // copy structer
    Mystring(const Mystring& str){}
	// move structer
    Mystring(Mystring&& str){}
};

template<typename M>
void test_moveable(M c, long& value){
    char buf[10];
	typedef typename iterator_traits<typename M::iterator>::value_type Vtype;
    clock_t timeStart = clock();
    for (long i = 0; i < value; ++i){
        snprintf(buf, 10, "%d", rand());	// 将随机数转化为字符
        auto ite = c.end();
        c.insert(ite, Vtype(buf));		// Vtype是一个萃取函数,它的返回值是一个右值
    }
}
```

当我们把`typename M 特例化Mystring`，以上当第20行函数`c.insert(ite, Vtype(buf))`在调用的时候`Vtype(buf) 会调用移动构造函数, 因为编译器会检测传递进来的是一个临时对象(右值)`

移动构造的过程类似于浅拷贝，只不过在浅拷贝的情况下进一步的将原来对象的调用权剥夺，见下图：

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/08/Snipaste_2020-08-20_21-17-35.png)

通过这种方式就减少了拷贝构造函数的调用从而也避免频繁的开辟空间和回收空间。

#### 完美转发
```C++
// 接收左值的函数 f()
template<typename T>
void f(T &)
{
	cout << "f(T &)" << endl;
}

// 接收右值的函数f()
template<typename T>
void f(T &&){
	cout << "f(T &&)" << endl;
}

template<typename T>
void PrintType(T&& param){ 
	f(param);  // 将参数param转发给函数 void f()
}


int main(){
	int num = 0;
	PrintType(num);		//传入左值
	PrintType(int(0));	//传入右值
}
```

```
// 输出
f(T&)
f(T&)
```

以上的输出让人觉得奇怪，在我们心中可能输出应该是一个`f(T&)`和一个`f(T&&)`但为何都是输出`f(T&)`?

这说明：**在任何函数的内部，对于形参的直接使用都是按照左值进行的。**

我们如何才能做到完美的转发呢? 这就需要用到`std::forward<T>()`

当我们把`PrintType()`中的`f()`函数改为:`f(std::forward<T>(param))`即可得到我们预先想的答案.

```C++
// 接收左值的函数 f()
template<typename T>
void f(T&){
    cout << "f(T &)" << endl;
}

// 接收右值的函数f()
template<typename T>
void f(T&&){
    cout << "f(T &&)" << endl;
}

template<typename T>
void PrintType(T&& param){
    f(std::forward<T>(param));  // 将参数param转发给函数 void f()
}

int main(){
    int num = 1;
    PrintType(num);     //传入左值
    PrintType(int(1));  //传入右值
}
```

```
// 输出
f(T&)
f(T&&)
```

