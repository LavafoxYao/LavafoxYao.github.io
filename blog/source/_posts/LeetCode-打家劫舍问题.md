---
title: LeetCode 打家劫舍问题
date: 2020-12-03 18:58:54
tags: ['LeetCode', 'DP']
categories: '题解'
---

`LeetCode`上打家劫舍的问题一共有三道，也是经典的`DP`入门题。

<!--more-->

### [198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

> 你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。
>
> 给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。
>
> **示例 1：**
>
> ```
> 输入：[1,2,3,1]
> 输出：4
> 解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
>      偷窃到的最高金额 = 1 + 3 = 4 。
> ```
>
> **示例 2：**
>
> ```
> 输入：[2,7,9,3,1]
> 输出：12
> 解释：偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
>      偷窃到的最高金额 = 2 + 9 + 1 = 12 。
> ```
>
> **提示：**
>
> - `0 <= nums.length <= 100`
> - `0 <= nums[i] <= 400`

**思路: 打家劫舍的问题存在两种状态**

1. 抢
2. 不抢

由于题目会有限制，一般的如不能连续抢劫相邻的房子，这样我们就需要用多一个状态位来表示房间的状态（抢或不抢）。



这样我们可以定义`dp[i][0]`表示第i间房子不偷时的最大值， `dp[i][1]`表示第i间房子偷时的最大值。



这样我们通过题目限制得到状态转移方程：`dp[i][1] = max(dp[i - 1][0] + nums[i], dp[i - 1][1]);`

```C++
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.empty())   return 0;
        if (nums.size() == 1)   return nums[0];
        if (nums.size() == 2)   return max(nums[0], nums[1]);
        // dp[i][0] 表示第i间房子不偷时的最大值
        // dp[i][0] = max(dp[i - 1][0], dp[i - 1][1])
        // dp[i][1] 表示第i间房子偷时的最大值
        // dp[i][1] = max(dp[i - 1][0] + nums[i], dp[i - 1][1]);
        vector<vector<int>> dp(nums.size(), vector<int>(2, 0));
        for (int i = 0; i < nums.size(); ++i){
            if (i == 0){    
                dp[0][0] = 0;
                dp[0][1] = nums[0];
            }
            else{
                dp[i][0] = max(dp[i - 1][0], dp[i - 1][1]);
                dp[i][1] = max(dp[i - 1][0] + nums[i], dp[i - 1][1]);
            }
        }
        return max(dp[nums.size() - 1][0], dp[nums.size() - 1][1]);
    }
};
```



### [213. 打家劫舍 II](https://leetcode-cn.com/problems/house-robber-ii/)

> 你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 **围成一圈** ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。**
>
> 给定一个代表每个房屋存放金额的非负整数数组，计算你 **在不触动警报装置的情况下** ，能够偷窃到的最高金额。
>
> **示例 1：**
>
> ```
> 输入：nums = [2,3,2]
> 输出：3
> 解释：你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [1,2,3,1]
> 输出：4
> 解释：你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
>      偷窃到的最高金额 = 1 + 3 = 4 。
> ```
>
> **示例 3：**
>
> ```
> 输入：nums = [0]
> 输出：0
> ```

**思路:** 和第一题思路大体一致，题目的场景换成了房屋是围成一个圈。



由于房子围城一个圈。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/12/rob.png)

这个小偷有两种走法：

1. 按上图的红线走 **( 0 -> 1 -> 2 )**  
2. 按上图的蓝线走 **( 1 -> 2 -> 3 )**

```C++
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.empty())   return 0;
        if (nums.size() == 1)   return nums[0];
        if (nums.size() == 2)   return max(nums[0], nums[1]);
        // 是否抢第一家
        int rob_first = rob(nums, 0, nums.size() - 2);
        int nrob_first = rob(nums, 1, nums.size() - 1);
        return max(rob_first, nrob_first);
    }

    int rob(const vector<int>& nums, int l, int h){
        if (l > h)  return 0;
        vector<vector<int>> dp(h - l + 1, vector<int>(2, 0));
        for (int i = 0, j = l; i < dp.size(); ++i, ++j){
            if (i == 0){
                dp[0][0] = 0;
                dp[0][1] = nums[j];
            }
            else{
                dp[i][0] = max(dp[i - 1][0], dp[i - 1][1]);
                dp[i][1] = max(dp[i - 1][0] + nums[j], dp[i - 1][1]);
            }
        } 
        return max(dp[dp.size() - 1][0], dp[dp.size() - 1][1]);
    }
};
```

### [337. 打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/)

> 在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。
>
> 计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。
>
> **示例 1:**
>
> ```
> 输入: [3,2,3,null,3,null,1]
> 
>      3
>     / \
>    2   3
>     \   \ 
>      3   1
> 
> 输出: 7 
> 解释: 小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.
> ```
>
> **示例 2:**
>
> ```
> 输入: [3,4,5,1,3,null,1]
> 
>      3
>     / \
>    4   5
>   / \   \ 
>  1   3   1
> 
> 输出: 9
> 解释: 小偷一晚能够盗取的最高金额 = 4 + 5 = 9.
> ```

**思路:** 这个题目的问题场景变成了一颗二叉树。但本质也只有两种状态： 1. 抢   2. 不抢

这也是我第一次做树状`DP`。



问题场景在「树」上，就要用到「树的遍历」，这里用「后序遍历」，这是因为：**我们的逻辑是子结点陆续汇报信息给父结点，一层一层向上汇报，最后在根结点汇总值**。

具体的题解可以看这里：[liweiwei1419](https://leetcode-cn.com/problems/house-robber-iii/solution/shu-xing-dp-ru-men-wen-ti-by-liweiwei1419/)

```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int rob(TreeNode* root) {
        if (!root)  return 0;
        if (!root->left && !root->right)    return root->val;
        vector<int> ans = rob_dfs(root);
        return max(ans[0], ans[1]);
    }

    const vector<int> rob_dfs(TreeNode* root){
        if (!root)  return {0, 0};
        vector<int> l = rob_dfs(root->left);
        vector<int> r = rob_dfs(root->right);
        // dp[0]表示不抢 dp[1]表示抢
        vector<int> dp(2, 0);
        // 根不抢的时候 要从子树中挑选最大值
        dp[0] = max(l[0], l[1]) + max(r[0], r[1]);
        // 根抢的时候 只能从左右子树没有抢的状态转换过来
        dp[1] = root->val + l[0] + r[0];
        return dp;
    }
};
```

