---
title: LeetCode-42.接雨水
date: 2020-04-04 19:44:13
tags:  LeetCode
categories: 题解
---

很经典的一题，第一次做不太明白这个题是什么意思。今天做每日一题的时候又遇到了，想做次总结，加深对该题的印象。

<!--more-->

#### [42. 接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

> 给定 *n* 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。
>
> ![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/04/Snipaste_2020-04-04_19-53-56.png)
>
> 上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 
>
> **示例:**
>
> ```
> 输入: [0,1,0,2,1,0,1,3,2,1,2,1]
> 输出: 6
> ```

该题目的意思就是我们常说的木桶效应，某个单位能接多少水，跟它左右两边最高的柱子中较低的柱子有关。

道理想明白就简单了，找左右两边最大值中小者，减去本身高度，再进行累加，就是能够接雨水的总量。

### 暴力法

第一次做的时候可以`ac`，但是第二次做的时候最后一个测试用例就超时了。

- max_element 函数原型

```C++
template< class ForwardIt > 
ForwardIt max_element(ForwardIt first, ForwardIt last );

template< class ForwardIt, class Compare >
ForwardIt max_element(ForwardIt first, ForwardIt last, Compare comp );
// first，end——区间范围(左闭右开)    comp——自定义比较函数
// 时间复杂度为O(n)
```

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        if (height.empty()) return 0;
        auto left = height.begin();
        auto right = height.end();
        int ans = 0;
        for (int i = 0; i < height.size(); ++i){
            int l = *max_element(left, left + i + 1);   // [) 所以 left + i 还要 + 1
            int r = *max_element(left + i, right);
            ans += min(l, r) - height[i];
        }
        return ans;
    }
};
```

### DP

用空间换时间，先将每个位置左边和右边的最大值算出来，每次在取的时候就不需要再向两边找了，这样就可以实现在查找的过程是`O（1）`的时间复杂度，最终的算法时间复杂度为`O(n)`

```C++
class Solution {
public:
    int trap(vector<int>& height) {
        if (height.empty()) return 0;
        int ans = 0;
        vector<int> l(height.size());
        vector<int> r(height.size());
        for (int i = 0; i < height.size(); ++i){
            if (i == 0)
                l[i] = height[i];
            else
                l[i] = max(l[i - 1], height[i]);
        }
        for (int i = height.size() - 1; i >= 0; --i){
            if (i == height.size() - 1)
                r[i] = height[i];
            else
                r[i] = max(r[i + 1], height[i]);
        }
        for (int i = 0; i < height.size(); ++i)
            ans += min(l[i], r[i]) - height[i];
    
        return ans;
    }
};
```

