---
title: LeetCode-215.数组中的第K个最大元素
date: 2020-03-10 10:58:05
tags: LeetCode
categories: 题解
---

遇到数组的题，实在没思路就先想着排序下，排完序可能就有思路了。对于挑选第几个数的题目一定要想到堆也就是`priority_queue`。

<!--more-->

#### [215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

>  在未排序的数组中找到第 **k** 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。
>
> **示例 1:**
>
> ```
> 输入: [3,2,1,5,6,4] 和 k = 2
> 输出: 5
> ```
>
> **示例 2:**
>
> ```
> 输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
> 输出: 4
> ```

### 堆解

维护一个`k`个元素大小的小顶堆，当元素超过k个`pop`掉，因为是小顶堆顶端元素是最小的，最后只有最大的k个元素被保存下来了，恰巧我们要求第K大的数，直接取`top()`就行。下图是示例1在小顶堆中的变化状态。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/03/Snipaste_2020-03-10_17-57-03.png)

```C++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        if(nums.size() == 0 || nums.size() < k)
            return -1;
        priority_queue<int, vector<int>, greater<int>> pq;      //priority_quequ在cpp中默认是大顶堆,小顶堆的定义如右
        for(int i = 0; i < nums.size(); ++i){
            pq.push(nums[i]);
            if(pq.size() > k)                                   //维护一个k个大小的堆
                pq.pop();						
        }    
        return pq.top();
    }
};
```



### 快速选择

[快速选择wiki](<https://zh.wikipedia.org/wiki/%E5%BF%AB%E9%80%9F%E9%80%89%E6%8B%A9>)

这个方法和`快速排序`思路是一致的将数组划分成两部分，只用到一边去找元素就行，不过这里要注意个问题就是，当`left` 和`right`在发生相遇之后会交错，在处理边界条件的时候需要特别注意。

```C++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        return quickSelect(nums, k, 0, nums.size() - 1);
    }
    int quickSelect(vector<int>& nums, int k, int start, int end){
        if(start == end)
            return nums[start];
        
        int left = start, right = end, mid = start + (end - start) / 2;
        int pivot = nums[mid];
        while(left <= right){
            while(left <= right && nums[left] > pivot)  //这里是选择第k大的元素相当于从大到小的排序注意这里的判定条件
                left++;
            while(left <= right && nums[right] < pivot)
                right--;
            if(left <= right)
                swap(nums[left++], nums[right--]);
        }
        if(start + k - 1 <= right)
            return quickSelect(nums, k, start, right);
        if(start + k - 1 >= left)
            return quickSelect(nums, k - (left - start), left, end);
        
        return nums[right+1];
    }
};
```

