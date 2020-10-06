---
title: LeetCode 75.颜色分类
date: 2020-10-03 10:56:07
tags: ['LeetCode', '三指针', '桶排序']
categories: 题解
---

著名的`荷兰国旗问题`

#### [75. 颜色分类](https://leetcode-cn.com/problems/sort-colors/)

<!--more-->

> 给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。
>
> 此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。
>
> **注意:**
> 不能使用代码库中的排序函数来解决这道题。
>
>  **示例:** 
>
> ```
> 输入: [2,0,2,1,1,0]
> 输出: [0,0,1,1,2,2]
> ```
>
> **进阶：**
>
> 一个直观的解决方案是使用计数排序的两趟扫描算法。
> 首先，迭代计算出0、1 和 2 元素的个数，然后按照0、1、2的排序，重写当前数组。
> 你能想出一个仅使用常数空间的一趟扫描算法吗？



**思路:** [荷兰国旗问题](https://en.wikipedia.org/wiki/Dutch_national_flag_problem)  其主要思想是给每个数字设定一种颜色，并按照荷兰国旗颜色的顺序进行调整。

### 方法一(桶排序)

```C++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        // 桶排
        int cnt_0 = 0, cnt_1 = 0, cnt_2 = 0;
        for (auto n : nums){
            if (n == 0)      cnt_0++;
            else if (n == 1) cnt_1++;
            else             cnt_2++;
        }
        int k = 0;
        for (int i = 0; i < cnt_0; ++i)
            nums[k++] = 0;
        for (int i = 0; i < cnt_1; ++i)
            nums[k++] = 1;
        for (int i = 0; i < cnt_2; ++i)
            nums[k++] = 2;
        return ;
    }
};
```



### 方法二(三指针)

可以看看官方给出的题解动态图 [官方题解](https://leetcode-cn.com/problems/sort-colors/solution/yan-se-fen-lei-by-leetcode/)



```C++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        if (nums.empty())   return ;
        int l = 0, cur = 0, r = nums.size() - 1;
        while (cur <= r){
            if (nums[cur] == 0){
                swap(nums[l], nums[cur]);
                l++;
                cur++;
            }
            else if (nums[cur] == 1)
                cur++;
            else{
            // 代码最难理解的部分就是这里为什么没有cur++
            // 因为cur左边的l在交换的时候已经被cur扫描过了
            // 能确保 cur的左边的全是0, 而r及其右边没有被cur扫过
            // 与cur 交换 还需再次检测nums[cur]是多少
                swap(nums[r], nums[cur]);
                r--;
            }
        }
    }
};
```

