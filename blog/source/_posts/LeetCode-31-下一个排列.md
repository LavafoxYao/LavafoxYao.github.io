---
title: LeetCode 31.下一个排列
date: 2020-07-12 22:58:10
tags: LeetCode
categories: 题解
---

[31. 下一个排列](https://leetcode-cn.com/problems/next-permutation/)

<!--more-->

> 实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。
>
> 如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。
>
> 必须原地修改，只允许使用额外常数空间。
>
> 以下是一些例子，输入位于左侧列，其相应输出位于右侧列。
> 1,2,3 → 1,3,2
> 3,2,1 → 1,2,3
> 1,1,5 → 1,5,1



`nextPermutation` 在`STL`中有对应的函数`next_permutation()`,调用库函数也是可以`AC`的.
```C++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        next_permutation(nums.begin(), nums.end());  //STL
    }
};
```
刷题是个学习的过程,我们怎么能痛失这次练习的机会呢(狗头).
思路:
> 1. 从后向前找第一个升序序列, 记住升序序列的第一个元素的位置
> 2. 从后向前找第一个大于升序序列的元素,将与其交换
> 3. 将交换点后的所有元素逆转
> 4. 若从后向前没有找到一个升序序列 说明整个序列为降序,只需逆转即可

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/07/Snipaste_2020-07-12_23-19-48.png)

```C++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        // 1, 3, 2, 7, 4  ==> 1, 3, 4, 2, 7
        // 从左边到右边 经历了什么? 
        int len = nums.size();
        if (len < 2)    return ;
        int i = len - 2;
        // 1. 从右到左,找到第一个升序 由上面的例子可以只我们找的是 2, 7这一个升序
        while (i >= 0 && nums[i] >= nums[i + 1])
            i--;
        // 2. 从右到左找第一个比2大的数,与2进行交换 ==> 由上面例子我们知道是 4
        // 经过第二步就变成: 1, 3, 2, 7, 4  ==> 1, 3, 4, 7, 2
        if (i >= 0){
            int j = len - 1;
            while (j > i && nums[j] <= nums[i])
                j--;
            swap(nums[i], nums[j]);
        }
        // 3. 把刚才找到的升序序列的终点(原来7的位置)到数组的终点reverse一下
        // 1, 3, 4, 7, 2 ==> 1, 3, 4, 2, 7
        reverse(nums.begin() + i + 1, nums.end());
    }
};
```

