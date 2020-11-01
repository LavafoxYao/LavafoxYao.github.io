---
title: 运用RAII的方式实现线程池
date: 2020-10-29 09:59:51
tags: ['多线程', '网络编程']
categories: 网络编程
---



最近这段时间在处理完实验室论文的事之后，都一直在学习如何去实现一个简易的`webserver`。今天来记录下学习到的重点知识。

<!--more-->

#### RAII是什么？

[引用：RAII 维基百科解释](https://zh.wikipedia.org/wiki/RAII)

**RAII**，全称**资源获取即初始化**（英语：Resource Acquisition Is Initialization），它是在一些面向对象语言中的一种惯用法。**RAII**源于C++，在Java，C#，D，Ada，Vala和Rust中也有应用。

**RAII要求，资源的有效期与持有资源的对象的生命期严格绑定，即由对象的构造函数完成资源的分配（获取），同时由析构函数完成资源的释放。在这种要求下，只要对象能正确地析构，就不会出现资源泄露问题。**

由于系统的各种资源都是有限的，我们必须准守一个原则：

1. 申请资源
2. 使用
3. 释放

但是由于代码的复杂性，我们很容易忽略第三步，又由于`C++`并没有像`JAVA`的`GC`机制，又要确保资源能够及时的回收。`RAII`的出现就极大的简化了资源管理，并能够保证程序的正确性和代码的简洁性。

### 线程池是什么？

[引用：线程池 维基百科解释](https://zh.wikipedia.org/wiki/%E7%BA%BF%E7%A8%8B%E6%B1%A0)

**线程池（英语：thread pool）**：一种线程使用模式。线程过多会带来**调度开销**，进而影响缓存局部性和整体性能。而线程池维护着多个线程，等待着监督管理者分配可并发执行的任务。这避免了在处理短时间任务时创建与销毁线程的代价。**线程池不仅能够保证内核的充分利用，还能防止过分调度**。可用线程数量应该取决于可用的并发处理器、处理器内核、内存、网络sockets等的数量。 例如，线程数一般取`cpu数量 + 2`比较合适，**线程数过多会导致额外的线程切换开销。**

线程池模式一般分为两种：HS/HA半同步/半异步模式、L/F领导者与跟随者模式。

- 半同步/半异步模式又称为**生产者消费者模式**，是比较常见的实现方式，比较简单。分为同步层、队列层、异步层三层。同步层的主线程处理工作任务并存入工作队列，工作线程从工作队列取出任务进行处理，如果工作队列为空，则取不到任务的工作线程进入挂起状态。由于线程间有数据通信，因此不适于大数据量交换的场合。
- 领导者跟随者模式，在线程池中的线程可处在3种状态之一：领导者leader、追随者follower或工作者processor。任何时刻线程池只有一个领导者线程。事件到达时，领导者线程负责消息分离，并从处于追随者线程中选出一个来当继任领导者，然后将自身设置为工作者状态去处置该事件。处理完毕后工作者线程将自身的状态置为追随者。这一模式实现复杂，但避免了线程间交换任务数据，提高了CPU cache相似性。



### 线程池的实现代码

 在C++中定义一个类时，如果不明确定义拷贝构造函数和拷贝赋值操作符，编译器会自动生成这两个函数。当我们希望禁止拷贝类的实例时，就不能用默认生成的这两个函数。 

```C++
// noncopyable.h
//
// Created by wuyao on 10/27/20.
//

#ifndef WEBSERVER_NONCOPYABLE_H
#define WEBSERVER_NONCOPYABLE_H
//不可拷贝类
class noncopyable{
public:
    noncopyable(const noncopyable&) = delete;
    noncopyable& operator = (const noncopyable&) = delete;
protected:
    noncopyable() = default;
    ~noncopyable() = default;
};

#endif //WEBSERVER_NONCOPYABLE_H


```

```c++
// mutexlock.h
// Created by wuyao on 10/27/20.
//

#ifndef WEBSERVER_MUTEXLOCK_H
#define WEBSERVER_MUTEXLOCK_H
#include "noncopyable.h"
#include <pthread.h>

// 继承了noncopyable保证了 显式调用构造函数不会再出现改类型的对象,确保了锁的唯一性
class MutexLock : public noncopyable{
public:
    MutexLock(){
        pthread_mutex_init(&m_mxt, NULL);   //只是初始化并没有加锁!!
    }

    ~MutexLock() {
        pthread_mutex_destroy(&m_mxt);
    }

    void lock(){
        pthread_mutex_lock(&m_mxt);
    }

    void unlock(){
        pthread_mutex_unlock(&m_mxt);
    }

    pthread_mutex_t* get_mutex_lock(){
        return &m_mxt;
    }

private:
    pthread_mutex_t m_mxt;          //互斥量
};

// 自动锁
class MutexGuard : public noncopyable{
public:
    //在MutexGuard的构造函数中加锁
    MutexGuard(MutexLock& lock):m_Mtx(lock){
        m_Mtx.lock();
    }
    //在MutexGuard的析构函数unlock
    ~MutexGuard(){
        m_Mtx.unlock();
    }
private:
    MutexLock &m_Mtx;           // 引用
};

#endif //WEBSERVER_MUTEXLOCK_H

```

```c++
// condition.h
// Created by wuyao on 10/27/20.
//

#ifndef WEBSERVER_CONDITION_H
#define WEBSERVER_CONDITION_H
#include "MutexLock.h"
#include "noncopyable.h"


class Condition{
public:
    Condition(MutexLock& mxt): m_mxt(mxt){
        pthread_cond_init(&m_cond, NULL);
    }

    ~Condition(){
        pthread_cond_destroy(&m_cond);
    }
    //等待条件成立
    // pthread_cond_wait内部的操作顺序是将线程放到等待队列，
    // 然后解锁，等条件满足时重新竞争锁，竞争到后加锁，然后返回。
    void wait(){
        pthread_cond_wait(&m_cond,m_mxt.get_mutex_lock());
    }
	
    // 唤醒某个线程
    void notify(){
        pthread_cond_signal(&m_cond);
    }
	// 唤醒所有线程
    void notify_all(){
        pthread_cond_broadcast(&m_cond);
    }

private:
    // 条件变量一定要和mutex一起使用
    // 注意这里使用的是引用
    MutexLock& m_mxt;
    pthread_cond_t m_cond;
};

#endif //WEBSERVER_CONDITION_H

```

```C++
// threadpool.h
// Created by wuyao on 10/27/20.
//

#ifndef WEBSERVER_THREADPOOL_H
#define WEBSERVER_THREADPOOL_H
#include "MutexLock.h"
#include "Condition.h"
#include <vector>
#include <list>
#include <functional>

#define MAX_THREAD_SIZE 512		
#define MAX_REQUEST_SIZE 1024

namespace thread{

    struct Task{
        std::function<void()> process;		// function C++11的关键字
    };

    class ThreadPool{
    public:
        ThreadPool(int thread_size);
        ~ThreadPool();

        void shutdown();						// 关闭线程池
        void append_task(Task*);				// 添加任务
        void run();								
    private:
        static void* worker(void *);			// 线程执行函数

        MutexLock m_mtx;
        Condition m_cond;
        int  m_thread_size;						
        int  m_cur_thread_cnt;					// 当前线程数
        bool m_shutdown;						// 是否关闭
        std::vector<pthread_t> m_thread;		// 消费者
        std::list<Task*> m_requst;				// 生产者
    };
}

#endif //WEBSERVER_THREADPOOL_H

```

```c++
// threadpool.cpp
// Created by wuyao on 10/27/20.
//

#include "include/ThreadPool.h"
#include <iostream>

using namespace thread;

ThreadPool::ThreadPool(int thread_size): m_thread_size(thread_size), m_cond(m_mtx), m_shutdown(false){
    if (thread_size <= 0 || thread_size > MAX_THREAD_SIZE)
        thread_size = 3;
    m_thread_size = thread_size;
    m_thread.resize(m_thread_size);
    for (int i = 0; i < m_thread_size; ++i){
        if (pthread_create(&m_thread[i], NULL, worker, this) != 0){
            std::cout<<"pthread_create error! file <"<<__FILE__<<"> at"<<__LINE__<<std::endl;
        }
        m_cur_thread_cnt++;
    }
    return ;
}

ThreadPool::~ThreadPool(){}

void ThreadPool::shutdown() {
    if (m_shutdown){
        std::cout<<"Already closed"<<std::endl;
    }
    m_cond.notify_all();
    for (int i = 0; i <= m_cur_thread_cnt;++i){
        if (pthread_join(m_thread[i], NULL) != 0)
            std::cout<<"pthread_join error file <"<<__FILE__<<"> at"<<__LINE__<<std::endl;
    }
    m_shutdown = true;
    std::cout<<"closed"<<std::endl;
    return ;
}

void* ThreadPool::worker(void *arg){
    ThreadPool* pool = static_cast<ThreadPool*>(arg);
    if (pool == NULL)   return nullptr;
    pool->run();
    return nullptr;
}


void ThreadPool::run(){
    while (1){
        Task *tsk = nullptr;
        {
            MutexGuard lock(m_mtx);
            // 这里存在一个虚假唤醒的问题
            // 当线程池未关闭且请求队列为空 等待请求队列
            while (!m_shutdown && m_requst.empty())
                m_cond.wait();
            tsk = m_requst.front();
            m_requst.pop_front();
            tsk->process();
        }
        if (tsk == nullptr) continue;
        delete(tsk);
    }
}
void ThreadPool::append_task(Task* tsk){
   if (tsk == nullptr)  return ;
   MutexGuard guard(m_mtx);
   if (m_requst.size() > MAX_REQUEST_SIZE){
       std::cout<<m_requst.size()<<std::endl;
       std::cout<<"this task request too much!"<<std::endl;
       return ;
   }
   m_requst.push_back(tsk);
   if (m_requst.size() >= 1)
       m_cond.notify();
   return ;
}
```

```C++
// main.cpp
#include "include/ThreadPool.h"
#include <iostream>

void print(int &i){
    std::cout<<"print() ==>"<<pthread_self()<<"  "<<i++<<std::endl;
}


int main() {
    int i = 0;
    thread::ThreadPool pool(4);
    while(1){
        thread::Task* tsk = new thread::Task;
        tsk->process = std::bind(print, std::ref(i));
        pool.append_task(tsk);
        sleep(1);
    }
    return 0;
}

```

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/10/Snipaste_2020-10-31_15-17-47.png)

**所谓虚假唤醒**,指的是即便我们没有 signal 相关的条件变量(即没有调用 `pthread_cond_signal`),等待(调用了` pthread_cond_wait`)的线程也可能被(虚假)唤醒,此时我们必须重新检查对应的标记值(以确认是否发生了(虚假)唤醒),又由于(虚假)唤醒可能会发生多次,所以我们最终需要使用循环来进行标记值检查. 

