---
title: LeetCode 204.计数质数
date: 2020-10-01 10:17:27
tags: ['LeetCode', '数学']
categories:	题解
---

虽然看起来是一道可以秒杀的简单题,但是再看数据规模我就知道不简单哦.

可以学习下算法中的经典思想**埃拉托斯特尼筛法** 

#### [204. 计数质数](https://leetcode-cn.com/problems/count-primes/)

<!--more-->

统计所有小于非负整数 *`n`* 的质数的数量。

>  **示例 1：** 
>
> ```
> 输入：n = 10
> 输出：4
> 解释：小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。
> ```
>
>  **示例 2：** 
>
> ```
> 输入：n = 0
> 输出：0
> ```
>
> **示例 3：**
>
> ```
> 输入：n = 1
> 输出：0
> ```
>
>  **提示：** 
>
> - `0 <= n <= 5 * 10^6`

### 方法1 (常规 超时解法)

```C++
class Solution {
public:
    int countPrimes(int n) {
        int ans = 0;
        // 当n > 2 肯定有2这个质数
        if (n > 2) ans++;
        // i+=2 ==> 大于3的偶数肯定不是质数
        for (int i = 3; i < n; i += 2){
            if (judge(i))
                ans++;
        }
        return ans;
    }
	
    bool judge(int n){
        for (int i = 2; i < n; ++i){
            if (n % i == 0)
                return false;
        }
        return true;
    }
};
```

### 方法2 (埃拉托斯特尼筛法)

```C++
class Solution {
public:
    int countPrimes(int n) {
        int ans = 0;
        if (n > 2) ans++;
        // 用作标记位
        vector<bool> flag(n, false);
        for (int i = 3; i < n; i += 2){
             // 当flag 为false说明其是质数
            if (!flag[i]){
            // 把质数的奇数倍全部设置为true(非质数)
                for (int j = 3; i * j < n; j += 2)
                    flag[i * j] = true;
                ans++;
            }
        }
        return ans;
    }
};
```

