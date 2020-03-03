---
title: 剑指offer-67.把字符串转换成整数
date: 2020-02-29 15:23:23
tags: 剑指offer
categories: 题解
---

[面试题67. 把字符串转换成整数](https://leetcode-cn.com/problems/ba-zi-fu-chuan-zhuan-huan-cheng-zheng-shu-lcof/)

<!--more-->

需要考虑到各种情况，第一次写很难一次性把所有的情况考虑到。题目虽然是medium，对算法能力的考验没多少，主要是考察的是bug free。

```C++
class Solution {
public:
    int strToInt(string str) {
      // "42"  输出: 42    "   -42" 输出: -42      "4193 with words" 输出: 4193   
      // "-91283472332" 输出: -2147483648 "words and 987" 输出: 0
      // "3.1415926" ==> "3"  不要忘记小数!!!!
      // ".1" ==> "0"			"+-2" ==> '0'   "   +0 123" ==>  0
      //   "-   234" ==> 0  "0-1"==>0		"++1" ==> "0"
        if (str.length() == 0)
            return 0;
        int ans = 0;
        int signal_tag = 0;
        int positive_tag = 0;
        bool change = false;
        //bool decimal_tag = false;

        for (int i = 0; i < str.length(); ++i) {
            if (str[i] == ' ' && ans == 0 && change == false && ((positive_tag == true) || (signal_tag == true)))
                return 0;
            if (str[i] == ' ' && ans == 0 && change == false)
                continue;
            if (str[i] == '+' && ans == 0 && change == false) {
                positive_tag++;
                continue;
            }
            if (str[i] == '-' && ans == 0 && change == false) {
                signal_tag++;
                continue;
            }
            if ((positive_tag && signal_tag) || positive_tag > 1 || signal_tag > 1)
                return 0;
            if ((str[i] - '0' > 9 || str[i] - '0' < 0) && ans == 0)
                return 0;
            if ((str[i] - '0' > 9 || str[i] - '0' < 0) && ans != 0)
                break;
            if (str[i]  == '.' && ans != 0)                     //处理小数
                return ans;
                
            //若ans >= INT_MAX /10  ans * 10就会越界
            if (ans == INT_MAX / 10 && signal_tag == false && (str[i] - '0' >= 7 && str[i] - '0' <= 9))
                return INT_MAX;
            if (-ans == INT_MIN / 10 && signal_tag == true && (str[i] - '0' >= 8 && str[i] - '0' <=9))
                return INT_MIN;
            if (ans > INT_MAX / 10 && signal_tag == false)
                return INT_MAX;
            if (-ans < INT_MIN / 10 && signal_tag == true)
                return INT_MIN;
            ans *= 10;
            change = true;
            int tmp = str[i] - '0';
            ans += tmp;
        }
        if (signal_tag % 2)
            return ans * (-1);
        return ans;

    }
};
```

