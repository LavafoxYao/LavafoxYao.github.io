---
title: 剑指offer-45.把数组排成最小的数
date: 2020-03-02 20:23:46
tags: 剑指offer
categories: 题解
---

在写题解之前，将学习到关于sort()的知识点先介绍下。

<!--more-->

### C++ std::sort()小结

##### 函数声明

```C++
template <class RandomIt, class Compare>
constexpr void sort(RandomIt first, RandomIt last, Compare comp);
```

需要注意的是`sort()`采用的是优化版本的快速排序，时间复杂度为`O(Nlog(N))`，且是不稳定的排序。

##### 参数说明

`first, last` 是要排序的元素的范围。

comp - 比较函数，需要满足：compare类型对象的函数调用运算的返回值，在按语境转换成`bool`时，若此类型所引入的严格弱序关系中调用的第一参数先出现于第二参数，则生成`true`，否则生成`false`。

一句话说明：compare(int a, int b)中，如果想从小到大排序，那么需要`return a < b`，如果想从大到小排序，则需要`return a > b;`

### [面试题45. 把数组排成最小的数](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

> 输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。
>
> **示例 1:**
>
> ```
> 输入: [10,2]
> 输出: "102"
> ```
>
> **示例 2:**
>
> ```
> 输入: [3,30,34,5,9]
> 输出: "3033459"
> ```
>
> 提示:
>
> 0 < nums.length <= 100
> 说明:
>
> 输出结果可能非常大，所以你需要返回一个字符串而不是整数
> 拼接起来的数字可能会有前导 0，最后结果不需要去掉前导 0

思路:将数组中的元素转换成字符串，例如：3和30 转换成"330"和"303"，然后通过比较组合后字符串的大小来重新确定数组中元素的顺序。

```C++
class Solution {
public:
    string minNumber(vector<int>& nums) {
        sort(nums.begin(), nums.end(), cmp);
        string str;
        for(int i = 0; i < nums.size(); ++i)
            str += to_string(nums[i]);
        return str;
    }
    static bool cmp(int a, int b){
        string A = to_string(a) + to_string(b);
        string B = to_string(b) + to_string(a);
        return A < B;
    }
};
```

