---
title: LeetCode 89.格雷编码
date: 2020-09-25 16:24:17
tags: ['LeetCode', '回溯', '找规律']
categories: 题解
---

屡做屡不会!!!! 特此记录。

<!--more-->

#### [89. 格雷编码](https://leetcode-cn.com/problems/gray-code/)

> 格雷编码是一个二进制数字系统，在该系统中，两个连续的数值仅有一个位数的差异。
>
> 给定一个代表编码总位数的非负整数 n，打印其格雷编码序列。即使有多个不同答案，你也只需要返回其中一种。
>
> 格雷编码序列必须以 0 开头。

 **示例 1:** 

```
输入: 2
输出: [0,1,3,2]
解释:
00 - 0
01 - 1
11 - 3
10 - 2

对于给定的 n，其格雷编码序列并不唯一。
例如，[0,2,3,1] 也是一个有效的格雷编码序列。

00 - 0
10 - 2
11 - 3
01 - 1
```

 **示例 2:** 

```
输入: 0
输出: [0]
解释: 我们定义格雷编码序列必须以 0 开头。
     给定编码总位数为 n 的格雷编码序列，其长度为 2n。当 n = 0 时，长度为 20 = 1。
     因此，当 n = 0 时，其格雷编码序列为 [0]。
```

### 格雷编码

在一组数的编码中，若任意两个相邻的代码只有一位二进制数不同，则称这种编码为**格雷码**（Gray Code），另外由于最大数与最小数之间也仅一位数不同，即“首尾相连”，因此又称**循环码**或**反射码**。 在数字系统中，常要求代码按一定顺序变化。例如，按自然数递增计数，若采用8421码，则数0111变到1000时四位均要变化，而在实际电路中，4位的变化不可能绝对同时发生，则计数中可能出现短暂的其它代码（1100、1111等）。在特定情况下可能导致电路状态错误或输入错误。使用格雷码可以避免这种错误。格雷码有多种编码形式。 [格雷编码的百度百科](https://baike.baidu.com/item/%E6%A0%BC%E9%9B%B7%E7%A0%81)

#### 解法1. 找规律

 **个数的格雷编码是 g[i] = i ^ (i >> 1)** 

```C++
class Solution {
public:
    vector<int> grayCode(int n) {
        // 因为格雷编码是两个连续的数值仅有一个位数的差异
        // 所以n位数一共有 2^n 个格雷编码
        vector<int> ans(1 << n);
        for (int i = 0; i < ans.size(); ++i)
            ans[i] = i ^ (i >> 1);      // 每次只变化1bit
        return ans;
    }
};
```

#### 解法2. 回溯
```C++
class Solution {
public:
    vector<int> grayCode(int n) {
        vector<int> ans;
        vector<bool> visited(1 << n, false);       // 共有 2^n 个格雷编码
        dfs(n, visited, 0, ans);
        return ans;
    }
    bool dfs(int n, vector<bool>& visited, int cur, vector<int>& ans){
        ans.push_back(cur);
        visited[cur] = true;
        // 当ans中的个数为2^n个 说明已经找完了
        if (ans.size() == 1 << n)   return true;
        for (int i = 0; i < n; ++i){
            // 改变一个bit
            int tmp = cur ^ (1 << i);
            if(!visited[tmp] && dfs(n, visited, tmp, ans))
                return true;
        }
        return false;
    }
};
```

