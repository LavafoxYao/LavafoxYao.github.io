---
title: LeetCode 402.移掉K位数字
date: 2020-07-25 13:45:11
tags: ['LeetCode', '贪心']
categories: 题解
---

#### [402. 移掉K位数字](https://leetcode-cn.com/problems/remove-k-digits/)

<!--more-->

> 给定一个以字符串表示的非负整数 num，移除这个数中的 k 位数字，使得剩下的数字最小。
>
> 注意:
>
> - num 的长度小于 10002 且 ≥ k。
> - num 不会包含任何前导零。
>
>  **示例 1 :** 
>
> ```
> 输入: num = "1432219", k = 3
> 输出: "1219"
> 解释: 移除掉三个数字 4, 3, 和 2 形成一个新的最小的数字 1219。
> ```
>
> **示例 2 :**
>
> ```
> 输入: num = "10200", k = 1
> 输出: "200"
> 解释: 移掉首位的 1 剩下的数字为 200. 注意输出不能有任何前导零。
> ```
>
> 示例 **3 :**
>
> ```
> 输入: num = "10", k = 2
> 输出: "0"
> 解释: 从原数字移除所有的数字，剩余为空就是0。
> ```
>
> 

思想: `贪心`, 思路在代码注释中有说明

```C++
class Solution {
public:
    string removeKdigits(string num, int k) {
        // 贪心思想 每次选取最小的数字 每选一个数字k--
        // 要注意处理前向0的情况       10200 k = 1  ==> 0200  ==> 200
        // 怎么使得每次选取的数字最小呢? 每次遍历到某个元素的时候
        // 都与已选数字的末端元素比较大小 如果已选数字较大则删除
        // 直到已选的元素比当前元素要小 或则 为空 又或则 k == 0
        if (num.empty())    return "0";
        string ans;
        for (int i = 0; i < num.size(); ++i){
            while (ans.size() > 0 && k > 0 && ans.back() > num[i]){
                k--;
                ans.pop_back();
            }
            ans.push_back(num[i]);
        }
        // 如果k没使用完 继续删除
        while (k > 0 && ans.size() > 0){
            ans.pop_back();
            k--;
        }
        // 处理前导0
        while (ans[0] == '0')
            ans.erase(ans.begin());
        if (ans.size() == 0)
            ans = "0";
        return ans;
    }
};
```

