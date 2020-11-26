---
title: C++之智能指针
date: 2020-11-26 18:48:05
tags: C++
categories: C++
---

智能指针属于C++中的重中之重了，趁着最近有点时间好好总结一下。
<!--more-->

##### 为什么要使用智能指针?

智能指针最大的作用就是:**管理在堆上申请的内存，避免内存泄露。**

因为在我们在写代码的时候，如果在堆上申请了内存，离开该作用于时却忘记释放这块内存，那么这块申请的内存只要进程一直在运行，我们就永远无法使用它，从而造成了内存泄露。



智能指针运用了`RAII`的机制，让我们管理内存变得简单。

##### 那么什么又是`RAII`机制呢？

简单的说就是：资源的有效期与持有资源的对象的生命期严格绑定，即由**对象的构造函数完成资源的分配**，用时由**对象的析构函数完成资源的释放**。在这种要求下，只要对象能够正确的析构，就不会出现资源泄露的问题。


### auto_ptr

auto_ptr出现在C++98的方案中，现已被C++11抛弃。



被抛弃的原因： auto_ptr负责维护一个对象指针，在自身析构的时候一定会通过delete方式释放非空指针。因此**auto_ptr不能管理对象数组指针**。为了避免重复释放，在auto_ptr实例之间赋值时，会转移托管指针的控制权，即在赋值完成后，托管指针变为nullptr。 

```C++
auto_ptr<string> p1(new string("hello"));
auto_ptr<string> p2;
p2 = p1;		// auto_ptr 可以正常编译.
```

上述代码可以正常的编译，但是会出现潜在的问题因为`p1`已经把所有权转交给`p2`，当我们再次访问`p1`时候会报错。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/Snipaste_2020-11-26_16-02-23.png)



所以auto_ptr的缺点是：**潜在着内存崩溃的问题**。



### unique_ptr

`unique_ptr`简单的说是`auto_ptr`的升级版。它实现了**严格拥有**的概念，**保证了同一时间内只有一个智能指针可以指向该对象**。

```C++
unique_ptr<string> p1(new string("hello"));
unique_ptr<string> p2;
p2 = p1;					// 语法错误.(vs 2019) [因为p1还存在于程序中,防止被引用]
```

编译器认为 `p2 = p1`是非法的，从而避免了`p1`被转移后指向`nullptr`的问题。因此`unique_ptr`较之`auto_ptr`更安全。



另外`unique_ptr`还有更聪明的地方: 当程序试图将`unique_ptr`赋值给另一个时：

	1. 如果源`unique_ptr`将要存在一段时间，那么编译器将禁止这么做。（上面例子说明了这个问题）

   	2. 如果源`unique_ptr`是**临时的右值**，那么编译器将允许这么做。(见下)

```C++
unique_ptr<string> p3;
p3 = unique_ptr<string> (new string ("world!"));	// 可以通过编译
```

#### unique_ptr的实现

```C++
template<typename T>
class UniquePtr {
private:
    T* _ptr;
public:
    UniquePtr(T* p = nullptr) : _ptr(p) {};
    // 禁止拷贝赋值
    UniquePtr(const UniquePtr& p) = delete;
    UniquePtr& operator=(const UniquePtr& p) = delete;
    // 支持移动拷贝
    UniquePtr(UniquePtr&& p): _ptr(p){
        p = nullptr;
    }

    UniquePtr& operator=(UniquePtr&& p) {
        if (this->_ptr != p._ptr) {
            if (this->_ptr)
                delete this->_ptr;
            this->_ptr = p._ptr;
            p._ptr = nullptr;
        }
        return *this;
    }

    T& operator*() { 
        assert(this->_ptr == nullptr);
        return *(this->_ptr); 
    }

    T* operator->() {
        assert(this->_ptr == nullptr);
        return this->_ptr; 
    }
    
    /*
    *   u.reset() 释放u指向的对象
    *   u.reset(q)如果提供了内置指针q,令u指向这个对象;否则将u置为空
    */
    void reset(T* q = nullptr) {
        if (q != this->_ptr) {
            if (this->_ptr)
                delete this->_ptr;
            this->_ptr = q;
        }
    }
    
	// u.release()  放弃u对指针的控制权,返回指针,并将u置为空
    T* release() {
        T* res = this->_ptr;
        this->_ptr = nullptr;
        return res;
    }

    T* get() { return this->_ptr; }

    void swap(UniquePtr& p) {
        if (this->_ptr != p._ptr)
            swap(this->_ptr, p._ptr);
    }
};

```

### shared_ptr

shared_ptr实现了共享式拥有的概念。**多个智能指针可以指向相同对象**，该对象可以和其相关资源会在“最后一个引用被销毁”的时候释放。（是不是有点像`inode`的引用计数，当`inode`引用计数为0的时候才会把`inode`指向的`data`删除）。



**「share_ptr 内部的应用计数是线程安全的，但对象的读取需要加锁」**



`share_ptr`不仅可以通过`new`来构造，还可以通过传入`auto_ptr`，`unique_ptr`，`weak_ptr`来构造。



`share_ptr`解决了`auto_ptr`在对象所有权上的局限性(独占式)，在使用引用计数的机制上提供了可以共享所有权的智能指针。



##### shared_ptr的实现

```C++
template<typename T>
class SharedPtr {
private:
    size_t _cnt;
    T* _ptr;
public:
    SharedPtr(T* ptr = nullptr) : _ptr(ptr) {
        if (_ptr == nullptr)    _cnt = 0;
        else
            _cnt = 1;
    }

    SharedPtr(const SharedPtr& ptr) {
        if (this->_ptr != ptr._ptr) {
            _ptr = ptr._ptr;
            _cnt = ptr._cnt;
            _cnt++;
        }
    }

    SharedPtr& operator=(const SharedPtr& ptr) {
        if (this->_ptr == ptr._ptr)
            return *this;
        if (this->_ptr) {
            this->_cnt--;
            if (this->_cnt == 0) {
                delete this->_ptr;
                this->_cnt = 0;
            }
        }
        this->_ptr = ptr._ptr;
        this->_cnt = ptr._cnt;
        this->_cnt++;
        return *this;
    }

    T& operator*() {
        assert(this->_ptr == nullptr);
        return *this;
    }

    T& operator->() {
        assert(this->_ptr == nullptr);
        return *this;
    }

    ~SharedPtr() {
        this->_cnt--;
        if (this->_cnt == 0) {
            delete this->_ptr;
            _cnt = 0;
        }
    }

    size_t use_count() {
        return _cnt;
    }

};
```

##### shared_ptr的缺陷 从上面的描述看来

`shared_ptr`已经非常的完美了，但是真的是这样吗？



我们拿个双向链表作为例子来说明`shared_ptr`造成循环引用

```C++
struct Node{
    int _data;
    shared_ptr<Node> _next;
    shared_ptr<Node> _prev;
};

int main()
{
    shared_ptr<Node> ptr_1(new Node);
    shared_ptr<Node> ptr_2(new Node);
  
    ptr_1->_next = ptr_2;
   	ptr_2->_prev = ptr_1;
   
    cout  << "ptr_1 ==> " << ptr_1.use_count() << "  ptr_2 ==> " << ptr_2.use_count() << endl;
    return 0;
}
```

由上我们可以看到` ptr_1->_next = ptr_2;` `ptr_l1`的引用计数增加1，变为2。`ptr_l2`同理。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/smart_point.png)

当我们要销毁`ptr_l1`时，我们必须使得它的引用计数在定义其的定义域内为1，因为离开作用域时候才会正常减1，使得其被销毁。



所以我们只能把`ptr_l1->next`销毁，但是`ptr_l1->next`是`ptr_l2`，它要被销毁必须使得`ptr_l2->prev`被销毁掉，所以就形成了一个相互僵持的状态（是不是很类似于死锁），导致两者都不能成功被销毁，从而致使内存泄露。

### weak_ptr

`weak_ptr`的作用就是避免`shared_ptr`陷入循环引用这种尴尬的局面。



`weak_ptr`是一种不控制对象生命周期的智能指针，它指向一个`shared_ptr`管理的对象。



`weak_ptr`只是提供了管理对象的一个访问手段，从另一方面来说它的指向并不会是的`shared_ptr`对象的引用计数加一。它是对对象的一种弱引用，不会增加对象的引用计数，和`shared_ptr`可以相互转换，`shared_ptr`也可以直接赋值给它，它也可以通过调用`lock()`来获得`shared_ptr`。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/11/smart_point2.png)



上面那种尴尬的局面可以把代码改成：

```C++
struct Node{
    int _data;
    weak_ptr<Node> _next;
    weak_ptr<Node> _prev;
};


int main()
{
    shared_ptr<Node> ptr_1(new Node);
    shared_ptr<Node> ptr_2(new Node);
  
    ptr_1->_next = ptr_2;
    ptr_2->_prev = ptr_1;
    
    cout  << "ptr_1 ==> " << ptr_1.use_count() << "  ptr_2 ==> " << ptr_2.use_count() << endl;
    return 0;
}

```



