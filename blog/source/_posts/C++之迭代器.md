---
title: C++之迭代器
date: 2019-10-08 12:09:47
tags: C++
categories: C++
---

# 迭代器

#### 提出迭代器概念的动机:
- 在软件构建过程中,集合对象内部结构常常变化各异。但对于这些集合对象，我们希望在不暴露其内部结构的同时，可以让外部客户代码透明地访问其中包含的元素；同时这种“透明遍历”也为“同一种算法在多种集合对象上进行操作”提供了可能。

GOF《设计模式》中迭代器模式的定义:
> 提供一种方法顺序访问一个聚合对象中的各个元素,而又不暴露(稳定)该对象的内部表示。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/%E8%BF%AD%E4%BB%A3%E5%99%A8%E6%A8%A1%E5%BC%8FUML.png)

迭代器是一种检查容器内元素并遍历元素的数据类型。
每种容器类型都定义了自己的迭代器类型，如vector

```c++
vector <int> in_vec(10,1);
vector <int>::iterator it = in_vec.begin();
// it的数据类型是由vector<int>定义
```
#### Iterator的作用

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/%E8%BF%AD%E4%BB%A3%E5%99%A8%E4%B8%8E%E7%AE%97%E6%B3%95%E7%9A%84%E5%85%B3%E7%B3%BB.png)

**Algorithms看不见Containers，对其一无所知。所以它需要的一切信息都必须从Iterator获得，而Iterator（由Containers提供）必须能够回答Algorithms的所有提问，才能搭配Algorithms的所有操作。**

Iterator必须能回答Algorithms关于Containers的iterator_category 、value_type、 difference_type、 reference、 pointer五种associated types。


#### 举例:List(单向)迭代器简单实现

```C++
template<typename T>
class List{
public:
    void inser_front(T value);
    void inser_end(T value);
    void dispaly(std::ostream &os = std:: cout) const;
    ListItem<T>* front() const {return _front;}
    ListItem<T>* end() const  {return _end;}
private:
	ListItem<T>* _end;
	ListItem<T>* _front;
	long 		 _size;
};
template <typename T>
class ListItem{
public:
	T value() const{return _value;}
	ListItem* next() const{return _next;}
	//....其他功能函数
private:
	T _value;
    ListItem* _next; 
};

template<typename Item>
struct ListIter{
   Item* ptr;
   
   ListIter(Item* p = 0) : ptr(p){} // default ctor
   //copy ctor ...编译器自带实现
   //operator= ...编译器自带实现
   
   Item& operator*() const {return *ptr;}
   Item* operator->() const{return ptr;}
   
   ListIter& operator++(){
   		//前置++
   		ptr = ptr->next;
   		return *this;
   }
	ListIter operator++(int){
		//后置++
		ListIter tmp = *this;
		++*this;
		return tmp;
	}
	bool operator==(const ListIter& i)const {return ptr == i.ptr;}
	bool operator!=(const ListIter& i)const {return ptr != i.ptr;}
};

void main()
{
    List<int> mylist;
    for(int i = 0; i < 5; ++i)
    {
        mylist.insert_front(i);
        mylist.insert_end(i+2);
    }
    mylist.display(); 	//10 {4 3 2 1 0 2 3 4 5 6}
    
    ListIter<ListItem<int>> begin(mylist.front());
    ListIter<ListItem<int>> end(mylist.end()); 
    ListIter<ListItem<int>> iter; 	//defalut 0 ,null
    iter = find(begin,end,3);
    if(iter == end)
    	cout<< "not found"<<endl;
    else
    	cout<<"found. "<<iter->value()<<endl;
    //执行结果: 3
    
    iter = find(begin, end, 7);
    if(iter == end)
     	cout<<"not found"<<endl;
    else
    	cout<<"found. "<<iter->value()<<endl;
    //执行结果:not found;
}
```
从以上实现可以看出，为了完成一个针对List而设计的迭代器，无可避免的暴露了太多List的实现细节；在main()中为了制作begin和end两个迭代器，我们暴露了ListItem；在ListIter class中为了实现operator++的目的，我们暴露了ListItem的操作函数next()。

#### 用面向对象实现迭代器

```c++
//利用面向对象实现迭代器
template<typename T>
class Iterator{
public:
    virtual void first() = 0;
    virtual void next() = 0;
    virtual bool isDone() const = 0;
    virtual T& current() = 0;
};

template<typename T>
class MyCollection{
 public:
    Iterator<T> GetIterator(){
        //...
    }
};

template<typename T>
class CollectionIterator : public Iterator<T>{
//面向对象的实例化
private:
    MyCollection<T> mc;
public:
    CollectionIterator(const MyCollection<T> &c) : mc(c){}
    
    void first() override{}
    void next() override{}
    bool isDone() const override{}
    T& current() override{}
};

void MyAlgorithm(){
    MyCollection<int> mc;
    
    Iterator<int> iter = mc.GetIterator();
    
    for(iter.first(); !iter.isDone();iter.next())
    {
        cout<< iter.current()<<endl;
    }
    
}
//面向对象的缺点:虚函数的调用是有成本的
//...
//虚函数是运行时多态,泛型编程是编译时多态.编译时多态性能高于运行时的多态
```

## 萃取机

STL的**核心思想**就是:将**数据容器和算法分开**，彼此**独立设计**，最后再以一帖胶着剂将它们撮合在一起。

 《C++标准库》有一句话说的很好，“iterator是一个抽象概念，任何东西，只要其行为像iterator，它就是iterator”。而不同的容器可能需要不同行进能力的iterator。


```c++
template<typename T, typename Ref, typename Ptr>
struct _list_iterator
{
    typedef bidirectional_iterator_tag iterator_category;
    typedef T 						    value_type;
    typedef Ptr							pointer;
    typedef Ref							reference;
    typedef ptrdiff_t					difference_type;
    //...
};
```

每个容器有各自的iterator，现实中C++的iterator设计过于繁琐，详解见 《C++标准库》，这里只是介绍下设计iterator中经典的思想trait技法;

#### 迭代器获取对象的类型
- 利用function template的参数推导机制:
```c++
template <typename I, typename T>
void fun_impl(I iter, T t)
{
	T temp; //这样可以通过模板的参数推导,推导出T的类型
	//...功能实现( func()函数的功能 )
}

template <typename I>
inline
void func(I iter)
{
	//func对外的接口
    func_impl(iter,*iter)
}

int main()
{
    int i;
    func(&i);
}
//这种类型推导的方式只能推导参数的类型,并不能推导返回值的类型
```
- 声明内嵌类型
```c++
template <typename T>
struct MyIter{
	typedef T value_type;  //内嵌类型声明(取别名)
	T* ptr;
	
	MyIter(T* p = 0) : ptr(p){};
	T& operator*() const {return *ptr;}
	//...
}

template <typename I>
typename I::value_type func(I iter)
{return *iter;}

int main()
{
    MyIter<int> iter(new int(8)); 
    cout<<func(iter);   //输出8(int类型)
}
//但是不是所有的迭代器都是结构体类型,如果是原生指针这种方式就不能实现了
```

- 偏特化(Partial Specialization)
```c++
//如果class template 拥有一个以上的template参数,我们可以针对其中某个(或数个,但非全部)tempalte参数进行特化.
template <typename T>
struct MyIter<T*>{                //偏特化版本,得到一个T*类型的原生指针
	typedef T value_type;         //内嵌类型声明(取别名)
	T* ptr;
	
	MyIter(T* p = 0) : ptr(p){};
	T& operator*() const {return *ptr;}
	//...
}

/*
//如果T的类型被const修饰如何?
//这就需要另外的特化版本
template <typename T>
struct MyIter<const T*>{          //偏特化版本,得到一个T*类型的原生指针
	typedef T value_type;         //内嵌类型声明(取别名)
	T* ptr;
	
	MyIter(T* p = 0) : ptr(p){};
	T& operator*() const {return *ptr;}
	//...
}
*/

template <typename I>
typename I::value_type func(I iter)
{return *iter;}

int main()
{
    MyIter<int> iter(new int(8)); 
    cout<<func(iter);   //输出8(int类型)
}


```
#### traits

**traits**:如果I有自己的value_type，那么通过这个traits的作用，萃取出来的value_type就是I::value_type。简单的说traits是一个中间层，目的就是为了获得变量的类型。

下图说明了trait扮演的"特性萃取机"角色，萃取各个迭代器的类型。

这个traints机器必须有能力分别它所获得的iterator是class iterator T 或是 native pointer T.

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/traits.png)

最常用的迭代器类型有五种:value_type 、 difference_type、 pointer、 reference、 iterator_category .

```c++
template<typename I>
struct iterator_traits{
    typedef typename I::iterator_category iterator_category;
    typedef typename I::value_type        value_type;
    typedef typename I::difference_type   difference_type;
    typedef typename I::pointer           pointer;
    typedef typename I::reference         reference;
    //typedef typename I:: ,I是无法表示原生指针的,因为原生指针根本没有包含其他类型,例如:vector<int>::iterator iter; I代表vector<int>.
};
//偏特化
template<typename T>  
struct iterator_traits<T*>      //native pointer
{
    typedef random_access_iterator_tag iterator_category;
	typedef T 				value_type;
    typedef ptrdiff_t 		difference_type;
    typedef T*				pointer;
    typedef T& 				reference;
};
template<typename T>
struct iterator_traits<const T*> 
{
	 typedef random_access_iterator_tag iterator_category;
	typedef T 				value_type;  
	//注意这是T而不是const T
    typedef ptrdiff_t 		difference_type;
    typedef const T*		pointer;
    typedef const T& 		reference;
}
```
以上这种不带成员变量和成员函数的类可以作为基类给子类来进行继承，这种的类的继承类似于inline的作用。

- value_type 是指迭代器所指对象的类型。

- difference_type用来表示两个迭代器之间的距离，因此它也可以用来表示一个容器的最大容量。

- reference_type，从"迭代器所指之物的内容是否允许改变"来看，迭代器分为两种：1.不允许改变"所指对象内容"称为constant iterators，例如const int* pic；2.允许改变“所指对象内容”，称为mutable iterators，例如：int* pi。

- pointer_type，指向迭代器所指之物

- iterator_category 迭代器的类型(移动性质)。input iterator 、output iterator  、forward iterator 、bidirectional iterator  、random access iterator。
#### 总结

- 迭代抽象:访问一个聚合对象的内容而无需暴露它的内部表示.
- 迭代多态:为遍历不同的集合结构提供一个统一的接口,从而支持同样的算法在不同的集合结构上进行操作.
- 迭代器的健壮性考虑:遍历的同时更改迭代器所在的集合结构,会导致问题.