---
title: LeetCode 229.求众数II
date: 2020-07-23 22:00:51
tags: ['LeetCode', '数组']
categories: 题解
---

#### [169. 多数元素](https://leetcode-cn.com/problems/majority-element/)

<!--more-->

> 给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。
>
> 你可以假设数组是非空的，并且给定的数组总是存在多数元素。

 **示例 1:** 

```
输入: [3,2,3]
输出: 3
```

 **示例 2:** 

```
输入: [2,2,1,1,1,2,2]
输出: 2
```



在解决这一题之前,我们先可以说下[169. 多数元素](https://leetcode-cn.com/problems/majority-element/)

这题用到了著名的`摩尔投票法` ,其核心思想是众数是大于一半的数,用其去和其他的数抵消.

#### 169.多数元素

```C++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        if (nums.empty())   return 0;
        int majority = 0, cnt = 0;
        for (int i = 0; i < nums.size(); ++i){
            if (majority == nums[i]){			// 先查看是否是 众数
                cnt++;
                continue;
            }
            if (cnt == 0){					    // 当计数器为0时更新众数
                majority = nums[i];
                cnt++;
                continue;
            }
            cnt--;								// 不是众数 则消减
        }
        return majority;

    }
};
```



#### 229.求众数II

这题的思路和上题本质是相同的,但是在处理细节上不同,由于求超过`n/3`次的元素,就可能存在两个`众数`,这样我们需要用两个计数器去计数.然后判断遍历的元素是否为所求的`众数`,若是则计数器自加,反之则自减.

```C++
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        if (nums.empty())   return {};
        int majority1 = 0, majority2 = 0;
        int cnt1 = 0, cnt2 = 0;
        vector<int> ans;
        for (int i = 0; i < nums.size(); ++i){
            if (major_num1 == nums[i]){
                cnt1++;
                continue;
            }
            if (major_num2 == nums[i]){
                cnt2 ++;
                continue;
            }
            if (cnt1 == 0){
                major_num1 = nums[i];
                cnt1 ++;
                continue;
            }
            if (cnt2 == 0){
                major_num2 = nums[i];
                cnt2 ++;
                continue;
            }
            // 上述情况都不是时
            cnt1--;
            cnt2--;
        }
        // 验证下'众数'  ===>因为可能存在只有一种'众数'的情况
        cnt1 = 0, cnt2 = 0;
        for (int i = 0; i < nums.size(); ++i){
            if (majority1 == nums[i])   cnt1++;
            else if (majority2 == nums[i])  cnt2++;
        }
        if (cnt1 > nums.size() / 3) ans.push_back(majority1);
        if (cnt2 > nums.size() / 3) ans.push_back(majority2);
        return ans;
    }
};
```

