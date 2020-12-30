---
title: LeetCode 丑数
date: 2020-12-30 18:52:06
tags: ['LeetCode', '数学', 'DP']
categories: ['题解']
---

丑数也是一道很经典的题目。



在LeetCode上一共有四道，我们今天只讲前三题，后面一题找时间再补上来吧。

<!--more-->

### [263. 丑数](https://leetcode-cn.com/problems/ugly-number/)

> 编写一个程序判断给定的数是否为丑数。
>
> 丑数就是只包含质因数 `2, 3, 5` 的**正整数**。
>
> **示例 1:**
>
> ```
> 输入: 6
> 输出: true
> 解释: 6 = 2 × 3
> ```
>
> **示例 2:**
>
> ```
> 输入: 8
> 输出: true
> 解释: 8 = 2 × 2 × 2
> ```
>
> **示例 3:**
>
> ```
> 输入: 14
> 输出: false 
> 解释: 14 不是丑数，因为它包含了另外一个质因数 7。
> ```
>
> **说明：**
>
> 1. `1` 是丑数。
> 2. 输入不会超过 32 位有符号整数的范围: [−2^31, 2^31 − 1]。

思路:其实这一题挺简单的，就是判断某个数是否只是由【2, 3, 5】作为因子组成，我们给出两种代码。

##### 迭代

```C++ 
class Solution {
public:
    bool isUgly(int num) {
        if (num == 0)   return false;	// 0并不是丑数
        if (num == 1)   return true;	// 1虽然不含[2,3,5] 但是也属于丑数
        while (1){
            if (num % 2 == 0)
                num /= 2;
            if (num % 3 == 0)
                num /= 3;
            if (num % 5 == 0)
                num /= 5;
            else
                break;
        }
        return num == 1 ? true : false;
    }
};
```

##### 递归

```C++
class Solution {
public:
    bool isUgly(int num) {
        if (num == 0)   return false;
        if (num == 1)   return true;
        return vaild(num);
    }

    bool vaild(int num){
        if (num == 2)   return true;
        if (num == 3)   return true;
        if (num == 5)   return true;
        if (num % 2 == 0)  return vaild(num / 2);
        if (num % 3 == 0)  return vaild(num / 3);
        if (num % 5 == 0)  return vaild(num / 5);
        return false;
    }
};
```

### [264. 丑数 II](https://leetcode-cn.com/problems/ugly-number-ii/)

> 编写一个程序，找出第 `n` 个丑数。
>
> 丑数就是质因数只包含 `2, 3, 5` 的**正整数**。
>
> **示例:**
>
> ```
> 输入: n = 10
> 输出: 12
> 解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。
> ```
>
> **说明:** 
>
> 1. `1` 是丑数。
> 2. `n` **不超过**1690。

思路:我贴上LeetCode上面大佬对这种思路的解释

[sunrise](https://leetcode-cn.com/problems/chou-shu-lcof/comments/)

我们知道，丑数的排列肯定是`1,2,3,4,5,6,8,10....` 然后有一个特点是，任意一个丑数都是由小于它的某一个丑数`*2`，`*3`或者`*5`得到的，

**那么如何得到所有丑数呢？** 现在假设有3个数组，分别是：

```C 
A：{1*2，2*2，3*2，4*2，5*2，6*2，8*2，10*2......}

B：{1*3，2*3，3*3，4*3，5*3，6*3，8*3，10*3......}

C：{1*5，2*5，3*5，4*5，5*5，6*5，8*5，10*5......}
```

那么所有丑数的排列，必定就是上面`A  B  C`3个数组的合并结果然后去重得到的，那么这不就转换成了三个有序数组的无重复元素合并的问题了吗？



而这三个数组就刚好是{1,2,3,4,5,6,8,10....}乘以2,3,5得到的。



合并有序数组的一个比较好的方法，**就是每个数组都对应一个指针，然后比较这些指针所指的数中哪个最小，就将这个数放到结果数组中，然后该指针向后挪一位。**



回到本题，要求丑数ugly数组中的第n项，而**目前只知道ugly[0]=1**，所以此时三个有序链表分别就只有一个元素：

```C
A ： {1*2......}

B ： {1*3......}

C ： {1*5......}
```

假设三个数组的指针分别是`i,j,k`，此时均是指向第一个元素，然后比较A[i]，B[j]和C[k]，得到的最小的数A[i]，就是ugly[1]，此时ugly就变成{1, 2}了，对应的ABC数组就分别变成了：

```C
A ： {1*2，2*2......}

B ： {1*3, 2*3......}

C ： {1*5,2*5......}
```



此时根据合并有序数组的原理，A数组指针i就指向了下一个元素，即'2*2'，而j和k依然分别指向B[0]和C[0]，然后进行下一轮合并，就是A[1]和B[0]和C[0]比较，最小值作为ugly[2].....如此循环n次，就可以得到ugly[n]了。



此外，注意到ABC三个数组实际上就是`ugly[]*2`，`ugly[]*3`和`ugly[]*5`的结果，所以每次只需要比较`A[i]=ugly[i]*2`，`B[j]=ugly[j]*3`和`C[k]=ugly[k]*5`的大小即可。然后谁最小，就把对应的指针往后移动一个，**为了去重，如果多个元素都是最小，那么这多个指针都要往后移动一个(一定要注意这一句话)。**

```C++
class Solution {
public:
    int nthUglyNumber(int n) {
        // 根据上面的思路讲解,你说这属于DP吗? 我也没有结论...
        vector<int> dp(n, INT_MAX);
        dp[0] = 1;
        int cnt_2 = 0, cnt_3 = 0, cnt_5 = 0;
        for (int i = 1; i < n; ++i){
            dp[i] = min(dp[cnt_2] * 2, min(dp[cnt_3] * 3, dp[cnt_5] * 5));
            // 这里有个细节就是一定要和所有的情况进行比较, 
            // 在不同方式得到相同丑数的时候只能取一种, 
            // 达到去重的效果
            if (dp[i] == dp[cnt_2] * 2)  cnt_2++;
            if (dp[i] == dp[cnt_3] * 3)  cnt_3++;
            if (dp[i] == dp[cnt_5] * 5)  cnt_5++;
        }
        return dp[n - 1];
    }
};
```

### [313. 超级丑数](https://leetcode-cn.com/problems/super-ugly-number/)

> 编写一段程序来查找第 `*n*` 个超级丑数。
> 超级丑数是指其所有质因数都是长度为 `k` 的质数列表 `primes` 中的正整数。
>
>  **示例:** 
>
> ```bash
> 输入: n = 12, primes = [2,7,13,19]
> 输出: 32 
> 解释: 给定长度为 4 的质数列表 primes = [2,7,13,19]，
> 前 12 个超级丑数序列为：[1,2,4,7,8,13,14,16,19,26,28,32] 。
> ```
>
> **说明:**
>
> 1 是任何给定 primes 的超级丑数。
>  给定 primes 中的数字以升序排列。
> 0 < k ≤ 100, 0 < n ≤ 10^6, 0 < primes[i] < 1000 。
> 第 n 个超级丑数确保在 32 位有符整数范围内。

思路: 超级丑数这一题是丑数II的一个泛化版本，在思路上基本是一模一样的。

```C++
class Solution {
public:
    int nthSuperUglyNumber(int n, vector<int>& primes) {
        vector<int> dp(n, INT_MAX);
        // cnt与丑数II中2 3 5 计数变量有着相同的作用
        vector<int> cnt(primes.size(), 0);
        dp[0] = 1;
        for (int i = 1; i < n; ++i){
            // 挑选出当前顺序中最小的
            for (int j = 0; j < primes.size(); ++j)
                dp[i] = min(dp[i], dp[cnt[j]] * primes[j]);
            // 把cnt计数 + 1;
            for (int j = 0; j < primes.size(); ++j)
                if (dp[i] == dp[cnt[j]] * primes[j])    cnt[j]++;
        }
        return dp[n - 1];
    }
};
```




