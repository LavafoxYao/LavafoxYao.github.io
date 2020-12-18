---
title: 剑指Offer 24.反转链表
date: 2020-12-18 10:47:26
tags: ['剑指Offer', '链表']
categories: ['题解']
---

反转链表是一道很经典的链表操作的题目，也很简单，至于我为什么要写一篇`blog`来记录这题呢?



我记得当初我一战考研复习数据结构的时候，看到书上用递归写的反转链表我一直都不太明白，如今我再刷一遍这题，我相信递归的方法我已经彻底懂了。

<!--more-->

#### [剑指 Offer 24. 反转链表](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)

定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。

> **示例:**
>
> ```
> 输入: 1->2->3->4->5->NULL
> 输出: 5->4->3->2->1->NULL
> ```
>
> **限制：**
>
> ```
> 0 <= 节点个数 <= 5000
> ```

### 迭代

```C++
 ListNode* reverseList(ListNode* head) {
        if (!head || !head->next)   return head;
        ListNode* pre = nullptr, *ptr = head;
        while (ptr){
            ListNode* ptr_next = ptr->next;
            ptr->next = pre;
            pre = ptr;
            ptr = ptr_next;
        }
        return pre;
    }
```



### 递归

递归才是我们这次的主题哦.



我们先给出部分代码:

```C++
ListNode* reverseList(ListNode* head) {
       if (!head || !head->next)    return head;
       ListNode* _next = head->next;
       ListNode* new_head = reverseList(_next);
    }
```

由上面代码可看到我们直到递归终止条件的时候，其实和普通的遍历没有很大的区别，遍历的方式如下图。



只不过当`_next`为`nullptr`时候触发递归终止条件，使得入栈的函数出栈.



![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/12/reverse_list_1.png)

**触发递归终止条件**

```C++
/*
* 这里需要说明下,由于我们采用的递归的方法,
* 所以每次函数出栈的时候,head永远是在_next的左边的
* 因为我们每次调用函数的时候使得_next在head的右边, 出栈的时候顺序是不会发生变化的
* 所以利用这个条件, 我们可以逐步的反转_next的指向,并且每次反转的时候使得head->next指向nullptr
*/
_next->next = head;
head->next = nullptr;
```



![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/12/reverse_list_2.png)


```C++
 ListNode* reverseList(ListNode* head) {
       if (!head || !head->next)    return head;
       ListNode* _next = head->next;
       ListNode* new_head = reverseList(_next);
       _next->next = head;
       head->next = nullptr;
       return new_head;
    }
```

