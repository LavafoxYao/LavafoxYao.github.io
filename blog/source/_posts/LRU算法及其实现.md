---
title: LRU算法及其实现
date: 2020-03-09 18:04:57
tags: ['LeetCode','计算机基础']
categories: ['题解', '计算机基础']
---

<!--more-->

## LRU 机制

在计算机存储层次结构中，低一层的存储器都可以看做是高一层的缓存。比如Cache是内存的缓存，内存是硬盘的缓存，硬盘是网络的缓存等等。

从本质上来说，缓存之所以有效是因为程序和数据的局部性。

Cache 技术所依赖的原理是"程序执行与数据访问的**局部性原理**"，这种局部性表现在两个方面：

1. **时间局部性**：如果程序中的某条指令一旦执行，不久以后该指令可能再次执行，如果某数据被访问过，不久以后该数据可能再次被访问。
2. **空间局部性**：一旦程序访问了某个存储单元，在不久之后，其附近的存储单元也将被访问，即程序在一段时间内所访问的地址，可能集中在一定的范围之内，这是因为指令或数据通常是顺序存放的。

Cache的容量是有限的，当Cache的空间都被占满后，如果再次发生缓存失效，就必须选择一个缓存块来替换掉。

**最久未使用算法（LRU, Least Recently Used）**：LRU法是依据各块使用的情况， 总是选择那个最长时间未被使用的块替换。这种方法比较好地反映了程序局部性规律。

## LeetCode LRU求解

#### [146. LRU缓存机制](https://leetcode-cn.com/problems/lru-cache/)

> 运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制。它应该支持以下操作： 获取数据 get 和 写入数据 put 。
>
> 获取数据 get(key) - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），否则返回 -1。
> 写入数据 put(key, value) - 如果密钥不存在，则写入其数据值。当缓存容量达到上限时，它应该在写入新数据之前删除最近最少使用的数据值，从而为新的数据值留出空间。
>
> **进阶:**
>
> 你是否可以在 **O(1)** 时间复杂度内完成这两种操作？
>
> **示例:**
>
> ```
> LRUCache cache = new LRUCache( 2 /* 缓存容量 */ );
> 
> cache.put(1, 1);
> cache.put(2, 2);
> cache.get(1);       // 返回  1
> cache.put(3, 3);    // 该操作会使得密钥 2 作废
> cache.get(2);       // 返回 -1 (未找到)
> cache.put(4, 4);    // 该操作会使得密钥 1 作废
> cache.get(1);       // 返回 -1 (未找到)
> cache.get(3);       // 返回  3
> cache.get(4);       // 返回  4
> ```

将上面的示例转化成下面的表格，可以更清楚明了的知道Cache中的状态。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/03/Snipaste_2020-03-09_20-49-38.png)

LRU中涉及到了查找和删除操作，能够在O（1）时间内完成查找和删除的操作的数据结构是不存在的。所以这里使用的是哈希表+双向链表的组合，哈希表作为get()函数的底层可以达到O(1)的时间复杂度，双链表和哈希表组合做删除操作的时候时间复杂度可以达到O（1）。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/03/Snipaste_2020-03-09_20-21-59.png)

```C++
class LRUCache {
public:
    LRUCache(int capacity) {
        _capacity = capacity;
    }
    
    int get(int key) {
        const auto iter = _map.find(key);
        if(iter == _map.end())
            return -1;
        // 在map中存在key,需要调整链表,将对应的节点移到头结点
        _cache.splice(_cache.begin(), _cache, iter->second);
        return iter->second->second;
    }
    
    void put(int key, int value) {
        const auto iter = _map.find(key);
        if(iter != _map.end()){                 // _map中有key,需要调整链表的头结点
            iter->second->second = value;       // value 不一定一定要等于 key
            _cache.splice(_cache.begin(), _cache, iter->second);
            return;
        }
        if(_cache.size() == _capacity){         // cache满了需要删除优先级最低的节点 也就是队尾的节点
            auto back_node = _cache.back();
            _map.erase(back_node.first);
            _cache.pop_back();
        }
        _cache.emplace_front(key, value);
        _map[key] = _cache.begin();

    }
    int _capacity;
    list<pair<int, int>> _cache;
    unordered_map<int, list<pair<int,int>>::iterator> _map;
};
```

