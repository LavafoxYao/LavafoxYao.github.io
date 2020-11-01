---
title: LeetCode 139.单词拆分
date: 2020-11-01 10:20:30
tags: ['DP', '递归', '记忆化']
categories: 题解
---

经典的`DP`与`记忆化递归` 的题,很值得一做.

<!--more-->

#### [139. 单词拆分](https://leetcode-cn.com/problems/word-break/)

> 给定一个**非空**字符串 *s* 和一个包含**非空**单词的列表 *wordDict*，判定 *s* 是否可以被空格拆分为一个或多个在字典中出现的单词。
>
> **说明：**
>
> - 拆分时可以重复使用字典中的单词。
> - 你可以假设字典中没有重复的单词。

 **示例 1：** 

```
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
```

 **示例 2：** 

```
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
     注意你可以重复使用字典中的单词。
```

**示例 3：**

```
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

## DP

用一个`dp数组`记录子串是否存在于`wordDict`中,其中`dp数组`最重要的作用是判断`s`被拆分的子串是否能完整的都出现在`wordDict`中

#### 方法一

```C++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        int len = s.length();
        // dp[i] 表示以s[i]结尾的字符串是否存在于wordDict中
        vector<bool> dp(len + 1, false);
        dp[0] = true;
        unordered_set<string> word_set(wordDict.cbegin(), wordDict.cend());
        for (int lo = 0; lo < len; ++lo){
            // 由于dp数组的目的是为了传递结果 
            // 所以如果dp[lo]本身不是true说明字符串没有连起来
            // 不符合我们要求解的结果
                if (dp[lo] == false)    continue;    
            for (int hi = lo + 1; hi <= len; ++hi){
                // 试探子串是否存在于 wordDict中
                string sub_s = s.substr(lo, hi - lo);
                // 若存在则标记hi为true
                if (word_set.count(sub_s))
                    dp[hi] = true;
            }
        }
        return dp[len];
    }
};
```

#### 方法二

方法二是将方法一优化了下,减少了比较的次数.

```C++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        int len = s.length();
        vector<bool> dp(len + 1, false);
        dp[0] = true;
        for (int lo = 0; lo < len; ++lo){
            if (dp[lo] == false) continue;
            for (auto w : wordDict){
                int hi = lo + w.length();
                if (hi <= len){
                    string sub_s = s.substr(lo, hi - lo);
                    if (sub_s == w)
                        dp[hi] = true;
                }
            }
        }
        return dp[len];
    }
};
```

## 记忆化递归

记忆化递归的本质是用一种数据结构去存储已经求解的结果(有点类似于DP的思想了),减少求解的次数,并且通过递归的方式逐渐趋近于答案.

```C++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> word_set(wordDict.cbegin(), wordDict.cend());
        return wordBreak(s, word_set);
    }

    bool wordBreak(const string& s, const unordered_set<string>& word_set){
        // 如果已经求解过了直接返回结果就好
        if (_res_mem.count(s))  return _res_mem[s];
        // 如果没求解过,但是word_set中存在该字符串则直接存储到_res_mem中去,
        // 并返回true
        if (word_set.count(s))  return _res_mem[s] = true;
        // 如果没求解过,切word_set中不存在s,则递归的求解s的子串
        for (int i = 0; i < s.length(); ++i){
            string lo = s.substr(0, i);
            string hi = s.substr(i);
            // 如果word_set中存在hi字符串且 lo递归求解为true
            // 说明字符串s是一个符合条件的字符串
            if (word_set.count(hi) && wordBreak(lo, word_set))
                return _res_mem[s] = true;
        }
        return _res_mem[s] = false;
    }

private:
    // 记录已经出现的字符串是否存在于worddict中 
    unordered_map<string, bool> _res_mem;   
};
```

