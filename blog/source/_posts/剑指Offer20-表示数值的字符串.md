---
title: 剑指Offer20.表示数值的字符串
date: 2020-09-02 23:09:38
tags: ['字符串', '剑指Offer', 'LeetCode']
categories: 练习
---

学习到的很简洁的解法.

<!--more-->

#### [剑指 Offer 20. 表示数值的字符串](https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/)

> 请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100"、"5e2"、"-123"、"3.1416"、"-1E-16"、"0123"都表示数值，但"12e"、"1a3.14"、"1.2.3"、"+-5"及"12e+5.4"都不是。
>

```C++
class Solution {
public:
    bool isNumber(string s) {
        trim(s);			// 去除头尾的空格
        int size = s.size();
        bool numSeen = false;
        bool eSeen = false;
        bool dotSeen = false;
        for (int i = 0; i < size; ++i){
            if (s[i] >= '0' && s[i] <= '9')
                numSeen = true;
            else if (s[i] == 'e' || s[i] == 'E'){
                // 已经出现过e
                if (eSeen || !numSeen)   return false;
                eSeen = true;       
                numSeen = false;    // e后面也需要numSeen
            }
            else if (s[i] == '.'){
                if (eSeen || dotSeen)   return false;
                dotSeen = true;
            }
            else if (s[i] == '+' || s[i] == '-'){
                if (i != 0 && (s[i - 1] != 'E'&& s[i - 1] != 'e')) return false;
            }
            else
                return false;
        }
        return numSeen;
    }
    
     void trim(string& s){
        int cnt = 0;
        for (int i = 0; i < s.size() && s[i] == ' '; ++i)
            cnt ++;
        if (cnt != 0)   s.erase(0, cnt);
        
        cnt = 0;
        for (int i = s.size() - 1; i >= 0 && s[i] == ' '; --i)
            cnt ++;
        if (cnt != 0)   s.erase(s.size() - cnt, cnt);
    }
};
```

