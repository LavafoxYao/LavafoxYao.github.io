---
title: LeetCode 135.分发糖果
date: 2020-12-24 18:55:49
tags: ['LeetCode','贪心']
categories: ['题解']
---

又是一道贪心题，又是不会的一题。

<!--more-->

#### [135. 分发糖果](https://leetcode-cn.com/problems/candy/)

> 老师想给孩子们分发糖果，有 N 个孩子站成了一条直线，老师会根据每个孩子的表现，预先给他们评分。
>
> 你需要按照以下要求，帮助老师给这些孩子分发糖果：
>
> - 每个孩子至少分配到 1 个糖果。
>
> - 相邻的孩子中，评分高的孩子必须获得更多的糖果。
>
> 那么这样下来，老师至少需要准备多少颗糖果呢?
>
> **示例 1:**
>
> ```
> 输入: [1,0,2]
> 输出: 5
> 解释: 你可以分别给这三个孩子分发 2、1、2 颗糖果。
> ```
>
> **示例 2:**
>
> ```
> 输入: [1,2,2]
> 输出: 4
> 解释: 你可以分别给这三个孩子分发 1、2、1 颗糖果。
>      第三个孩子只得到 1 颗糖果，这已满足上述两个条件。
> ```
>
> 

思路的话参考: [Krahets](https://leetcode-cn.com/problems/candy/solution/candy-cong-zuo-zhi-you-cong-you-zhi-zuo-qu-zui-da-/)

```C++
class Solution {
public:
    int candy(vector<int>& ratings) {
        if (ratings.empty())   return 0;
        int size = ratings.size();
        vector<int> left(size, 1);
        vector<int> right(size, 1);
        for (int i = 1; i < size; ++i){
            if (ratings[i - 1] < ratings[i])  left[i] = left[i - 1] + 1;
        }
        
        for (int i = size - 2; i >= 0; --i){
            if (ratings[i + 1] < ratings[i]) right[i] = right[i + 1] + 1; 
        }
        
        int ans = 0;
        for (int i = 0; i < size; ++i){
            ans += max(left[i], right[i]);
        }
        return ans;
    }
};
```

