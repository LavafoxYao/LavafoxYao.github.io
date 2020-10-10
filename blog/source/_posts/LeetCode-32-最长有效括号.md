---
title: LeetCode 32.最长有效括号
date: 2020-10-10 10:29:38
tags: ['LeetCode', 'Stack', 'DP']
categories: ['题解']
---

很经典的一道`DP`求最大值的问题。

也可以巧妙的用`stack`去求解，但是我们在学习过程中一定要掌握最根本的解法，不要去投机取巧学习很多奇技淫巧。

<!--more-->

#### [32. 最长有效括号](https://leetcode-cn.com/problems/longest-valid-parentheses/)

> 给定一个只包含 `'('` 和 `')'` 的字符串，找出最长的包含有效括号的子串的长度。
>
> **示例 1:**
>
> ```
> 输入: "(()"
> 输出: 2
> 解释: 最长有效括号子串为 "()"
> ```
>
> **示例 2:**
>
> ```
> 输入: ")()())"
> 输出: 4
> 解释: 最长有效括号子串为 "()()"
> ```

### 方法一（stack）
**思路** ：用一个栈来存储`string`中的括号在`string`中的下标，当`s[i] == )`就有可能形成有效括号对，当栈顶元素为对应的字符为（  `s[st.top()] == (`则说明是有效括号对，则将栈顶元素弹出，用当前下标减去当前栈顶元素存储的下标，因为在栈中的元素是还未成功匹配的括号，已经匹配成功的元素已经出栈了，这里要处理当栈为空的特殊情况。
```C++
class Solution {
public:
    int longestValidParentheses(string s) {
    	// stack
        int size = s.size();
        if (size == 0)  return 0;
        // stack用以记录string 元素的下标
        stack<int> st;
        int ans = 0;
        for (int i = 0; i < size; ++i) {
            // '(' 无一例外全部记录进栈下标
            if (s[i] == '(')
                st.push(i);
            else {
                if (!st.empty() && s[st.top()] == '(') {
                    st.pop();
                    int cnt = i - (st.empty() ? -1 : st.top());
                    ans = max(ans, cnt);
                }
                else
                    st.push(i);
            }
        }
        return ans;
    }
};

```
### 方法二(DP)

DP的思路我是参考如下大佬的想法：

[宝宝可乖了](https://leetcode-cn.com/problems/longest-valid-parentheses/solution/zui-chang-you-xiao-gua-hao-xu-lie-by-bao-bao-ke-gu/)

```C++
class Solution {
public:
    int longestValidParentheses(string s) {
        int size = s.size();
        if (size == 0)  return 0;
        // dp[i] 表示以s[i]结尾的最长有效括号的长度
        // dp[i - 1] ==> i - dp[i - 1] - 1 表示最近未匹配的'('
        // dp[i] = i - (i - dp[i - 1] - 1) + 1 + dp[i - dp[i - 1] - 2]
        vector<int> dp(size, 0);
        int ans = 0;
        for (int i = 1; i < size; ++i){
            if (s[i] == ')'){
                int start = i - dp[i - 1] - 1;
                if (start >= 0 && s[start] == '(')
                    dp[i] = i - start + 1 + 
                        (start - 1 >= 0 ? dp[start - 1] : 0);           
                ans = max(ans, dp[i]);
            }
        }
        return ans;
    }
};
```