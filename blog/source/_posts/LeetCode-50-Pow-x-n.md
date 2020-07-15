---
title: 'LeetCode 50.Pow(x, n)'
date: 2020-07-15 21:23:04
tags: ['LeetCode', '二分法']
categories: 题解
---

#### [50. Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)

<!--more-->

> 实现 pow(x, n) ，即计算 x 的 n 次幂函数。
>
> **示例 1:**
>
> ```
> 输入: 2.00000, 10
> 输出: 1024.00000
> ```
>
> **示例 2:**
>
> ```
> 输入: 2.10000, 3
> 输出: 9.26100
> ```
>
> **示例 3:**
>
> ```
> 输入: 2.00000, -2
> 输出: 0.25000
> 解释: 2-2 = 1/22 = 1/4 = 0.25
> ```
>
> **说明:**
>
> - -100.0 < x < 100.0
> - n 是 32 位有符号整数，其数值范围是 [−2^31, 2^31 − 1] 。

**思路**：调用库函数pow()....速度最快,代码最短(手动狗头)

```C++
class Solution {
public:
    double myPow(double x, int n) {
        return pow(x, n);
    }
};
```

**思路**: 二分法

例如: 求解2的9次方.

> 1. n = 9        ans = 1       x = 2
> 2. n = 4        ans = 2      x = 2^2
> 3. n = 2        ans = 2      x= 2 ^4
> 4. n = 1        ans = 2^9

从上面可以看到什么规律吗?

我们每次求解的过程都把**数据规模缩小了一半**,这样就提升了算法的效率.

上述有2个**细节**

1. 当n为奇数的时候需要多乘以个x
2. 当n为负数的时候犹豫0的存在 负数直接取反变正数可能会存不下,所以这里要用`long`

```C++
class Solution {
public:
    double myPow(double x, int n) {
        long N = n;		//int范围是[-2^31,2^31-1]负数的表示范围比正数大，所以不能简单取绝对值
        if (n < 0){
            x = 1 / x;
            N = -N;
        }
        double ans = 1.0;
        double cur = x;
        for (long i = N; i > 0; i /= 2){
            if (i % 2) 	ans *= cur;			// 当n为奇数时
            cur *= cur;
        }
        return ans;
    }
};
```

