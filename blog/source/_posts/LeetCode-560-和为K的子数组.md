---
title: LeetCode 560.和为K的子数组
date: 2020-07-31 00:09:37
tags: ['LeetCode', '数组', '前缀和', 'Hash']
categories: 题解
---

一道非常非常经典的利用前缀和求解的题目!!!

#### [560. 和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

<!--more-->

>   给定一个整数数组和一个整数 **k，**你需要找到该数组中和为 **k** 的连续的子数组的个数。 
>
>  **示例 1 :** 
>
> ```
> 输入:nums = [1,1,1], k = 2
> 输出: 2 , [1,1] 与 [1,1] 为两种不同的情况。
> ```
>
> **说明 :**
>
> 1. 数组的长度为 [1, 20,000]。
> 2. 数组中元素的范围是 [-1000, 1000] ，且整数 **k** 的范围是 [-1e7, 1e7]。

**思路:**

1. 暴力枚举法
2. 前缀和

### 枚举法

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/07/Snipaste_2020-07-31_00-27-25.png)

有个小技巧,在枚举的过程中从起点到终点这样计算可以减少计算的次数.由于访问了`1+2+...+N`次数组,所以时间复杂度为`O(n^2)`

```C++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        if (nums.empty())   return 0;
        int sum = 0, ans = 0;
        int lo = 0, hi = nums.size() - 1;
        while (lo <= hi){
            int cur = lo;
            sum = 0;
            while (cur <= hi){
                sum += nums[cur];
                cur++;
                if (sum == k)  ans++;
            }
            lo++;
        }
        return ans;
    }
};
```

### 前缀和

前缀和:前缀和是一种重要的预处理，能大大降低查询的时间复杂度。我们可以简单理解为“数列的前 n 项的和”。

利用前缀和我们可以很快的得到某个区间的和.

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/07/Snipaste_2020-07-31_00-50-49.png)



![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/07/Snipaste_2020-07-31_00-51-13.png)

某个子数组的和,也就是某两个前缀和之差.我们可以得到这样的一个公式`pre_sum[lo] + pre_sum[hi] == k`(hi >= lo) ,为了方便起见我们可以把公式转换成`pre_sum[hi] - k == pre_sum[lo]`,又由于我们是从前往后遍历求解前缀和,可以把中间求解的前缀和放到一个`hashtable`中使得能够进行`O(1)`的查找.

这样可以使得时间复杂度将至`O(n)`

```C++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        if (nums.empty())   return 0;
        int ans = 0, pre_sum = 0;
        unordered_map<int, int> m_hash;
        m_hash[0] = 1;                      // 当前缀和为0的时候不需要特殊处理
        for (int i = 0; i < nums.size(); ++i){
            pre_sum += nums[i];             // 保持当前的前缀和
            if (m_hash.count(pre_sum - k)) // 判定hash/map中存在不要用 [] == 0 
                ans += m_hash[pre_sum - k];
            m_hash[pre_sum] ++;
        }
        return ans;
    }
};
```

