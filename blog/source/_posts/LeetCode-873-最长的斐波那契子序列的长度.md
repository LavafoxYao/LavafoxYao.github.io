---
title: LeetCode 873.最长的斐波那契子序列的长度
date: 2020-10-08 10:58:39
tags: ['LeetCode', 'DP']
categories: '题解'
---

与上篇`blog`[最长等差数列](https://lavafoxyao.github.io/2020/10/08/LeetCode-1027-%E6%9C%80%E9%95%BF%E7%AD%89%E5%B7%AE%E6%95%B0%E5%88%97/#more)题目类似，解题方法也相同。

<!--more-->

#### [873. 最长的斐波那契子序列的长度](https://leetcode-cn.com/problems/length-of-longest-fibonacci-subsequence/)

> 如果序列 X_1, X_2, ..., X_n 满足下列条件，就说它是斐波那契式的：
>
> - n >= 3
>
> - 对于所有 i + 2 <= n，都有 X_i + X_{i+1} = X_{i+2}
>
> 给定一个严格递增的正整数数组形成序列，找到 A 中最长的斐波那契式的子序列的长度。如果一个不存在，返回  0 。
>
> （回想一下，子序列是从原序列 A 中派生出来的，它从 A 中删掉任意数量的元素（也可以不删），而不改变其余元素的顺序。例如， [3, 5, 8] 是 [3, 4, 5, 6, 7, 8] 的一个子序列）
>
>  **示例 1：** 
>
> ```
> 输入: [1,2,3,4,5,6,7,8]
> 输出: 5
> 解释:
> 最长的斐波那契式子序列为：[1,2,3,5,8] 。
> ```
>
>  **示例 2：** 
>
> ```
> 输入: [1,3,7,11,12,14,18]
> 输出: 3
> 解释:
> 最长的斐波那契式子序列有：
> [1,11,12]，[3,11,14] 以及 [7,11,18] 。
> ```
>
>  **提示：** 
>
> - `3 <= A.length <= 1000`
> - `1 <= A[0] < A[1] < ... < A[A.length - 1] <= 10^9`
> - *（对于以 Java，C，C++，以及 C# 的提交，时间限制被减少了 50%）*
>
> 

**思路**：与最长等差数列的题目思想类似，但是这一题给的数据规模是`1 <= A[0] < A[1] < ... < A[A.length - 1] <= 10^9`就不能用`array`作为`hashtable`了。

```C++
class Solution {
public:
    int lenLongestFibSubseq(vector<int>& A) {
        if (A.empty())  return 0;
        int size = A.size();
        // dp[i][j] 表示以A[i] A[j] 顺序结尾的斐波那契序列的长度
        // target = A[j] - A[i]
        // 要判断target是否在i之前就存在 ==》 hashtable存储结果
        // dp[i][j] = dp[hashtable[target]][i] + 1
        vector<vector<int>> dp(size, vector<int>(size, 2));
      
        unordered_map<int, int> m_map;
        for (int i = 0; i < size; ++i)
            m_map[A[i]] = i;
        int ans = 2;
        for (int i = 0; i < size; ++i){
            for (int j = i + 1; j < size; ++j){
                int target = A[j] - A[i];
                // 确保 target A[i] A[j] 这个序列
                // 由于A是个严格递增的序列
                if (target >= A[i])  break;
                // target可能不存在m_map中 直接 m_map.at[target]会异常
                auto iter = m_map.find(target);
                if (iter == m_map.end()) continue;
                dp[i][j] = dp[m_map[target]][i] + 1;
                ans = max(ans, dp[i][j]);
            }
        }
        // 斐波那契序列最短长度为3 若为2 则不存在该种序列
        return ans == 2 ? 0 : ans;
    }
};
```

