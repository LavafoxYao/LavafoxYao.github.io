---
title: 剑指Offer 43.1~n整数中1出现的次数
date: 2020-12-05 11:27:42
tags: 剑指offer
categories: 题解
---

这题是我个人感觉剑指offer里面最恶心的题目了，没有之一。

<!--more-->

#### [剑指 Offer 43. 1～n 整数中 1 出现的次数](https://leetcode-cn.com/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/)

> 输入一个整数 n ，求1～n这n个整数的十进制表示中1出现的次数。
>
> 例如，输入12，1～12这些整数中包含1 的数字有1、10、11和12，1一共出现了5次。
>
>  
>
> **示例 1：**
>
> ```
> 输入：n = 12
> 输出：5
> ```
>
> **示例 2：**
>
> ```
> 输入：n = 13
> 输出：6
> ```
>
> **限制：**
>
> - `1 <= n < 2^31`



思路：是参考[Huifeng Guan](https://www.youtube.com/channel/UCY5Z0of98W-YSdmPgAe1DaA) 



给个具体点的例子来说明这个思路吧.



例如: 我们选取  **41201**



我们可以通过算得每位上出现1的次数累加之后就可以得到我们要的结果了



那么我们怎么算的每一位究竟有多少位1呢?
![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/12/tu2.png)
**怎么确认低位的值呢?**

分三种情况讨论

1. 百位的值大于1, 那么个位和十位上所有的1都是您的! 那么低位共有 pow(3 - 1, 10)个1
2. 百位的值等于1, 那么个位和十位上的1并不都是您的,只有小于 101的值是您的 所以一共有个1, 101中的百位上的1,  还有一个就是当前数字百位上的1
3. 百位上的值等于0, 那么个位和十位上的1全都不是您的

这是讨论每个位上拥有的1的个数,同理 千位 万位上的1也是如此计算, 然后把所有的1累加起来就是答案.




```C++
class Solution {
public:
    int countDigitOne(int n) {
        // 可能出现负数
        if (n < 0)  return 0;
        string str = to_string(n);
        // 从个位==> 高位, 所以reverse
        reverse(str.begin(), str.end());
        long long ans = 0;
        for (int i = 1; i <= str.size(); ++i){
            // hi_num为高位的数字 ==>4321  ==> 432
            long long hi_num = n / (long long)pow(10, i);
            //  432 * 10^0  ==> 432[1]   1的左边为0位
            ans += hi_num * (long long)pow(10, i - 1);
			// 获取当前的值
            int cur_num = str[i - 1] - '0';
            if (cur_num > 1)
                ans +=  (long long)pow(10, i - 1);
            // 取余数 ==> 1 所以只有1个101 上的百位上的值 + 上自身的1
            else if (cur_num == 1)
                ans += n %(long long)pow(10, i - 1) + 1;
            else if (cur_num == 0)
                ans += 0;
        }
        return ans;
    }
};
```

