---
title: LeetCode 148.排序链表
date: 2020-09-30 14:47:09
tags: ['LeetCode', '链表']
categories: 题解
---

利用归并的思想去排序链表.

特别是`bottom to up`还是很有难度的.

#### [148. 排序链表](https://leetcode-cn.com/problems/sort-list/)

<!--more-->

在 *O*(*n* log *n*) 时间复杂度和常数级空间复杂度下，对链表进行排序。

>**示例 1:**
>
>```
>输入: 4->2->1->3
>输出: 1->2->3->4
>```
>
>**示例 2:**
>
>```
>输入: -1->5->3->4->0
>输出: -1->0->3->4->5
>```

### 方法1 递归的merge

思路可以参考[合并链表](https://lavafoxyao.github.io/2020/08/31/%E5%90%88%E5%B9%B6%E9%93%BE%E8%A1%A8/#more)

```C++
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        // spilt
        // 如果为空或则只有一个元素就停止spilt
        if (!head || !head->next)   return head;
        ListNode* slow = head, *fast = head->next;
        while (fast && fast->next){
            fast = fast->next->next;
            slow = slow->next;
        }
        ListNode* tmp_head = slow->next;
        slow->next = nullptr;
        return merge(sortList(head), sortList(tmp_head));
    }

    ListNode* merge(ListNode* l1, ListNode* l2){
        if (!l1)    return l2;
        if (!l2)    return l1;
        if (l1->val < l2->val)
            l1->next = merge(l1->next, l2);
        else
            l2->next = merge(l1, l2->next);
        return l1->val < l2->val ? l1 : l2;
    }
};
```

### 方法2 非递归的merge (up to bottom)

时间复杂度: `O（nlogn）`

空间复杂度:`o(n)` (算上递归用的栈空间)

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        // spilt
        // 如果为空或则只有一个元素就停止spilt
        if (!head || !head->next)   return head;
        ListNode* slow = head, * fast = head->next;
        while (fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
        }
        ListNode* tmp_head = slow->next;
        slow->next = nullptr;
        return merge(sortList(head), sortList(tmp_head));
    }
    
	// 非递归的merge
    ListNode* merge(ListNode* l1, ListNode* l2) {
        if (!l1)    return l2;
        if (!l2)    return l1;
        ListNode dummy(-1);
        ListNode* ptr = &dummy;
        while (l1 || l2) {
            if (l1 && l2 && l1->val < l2->val) {
                ptr->next = l1;
                l1 = l1->next;
            }
            else if(l2 && l1 && l1->val >= l2->val) {
                ptr->next = l2;
                l2 = l2->next;
            }
            else if (l1) {
                ptr->next = l1;
                l1 = l1->next;
            }
            else {
                ptr->next = l2;
                l2 = l2->next;
            }
            ptr = ptr->next;
        }
        return dummy.next;
    }
};
```

### 方法3 非递归的merge(bottom to up)

由于没有使用递归和额外空间所以空间复杂度是`O(1)`, 时间复杂度为`O(nlogn)`

```C++
// 要手动模拟split的过程
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        if (!head || !head->next) return head;

        int size = 0;
        ListNode* ptr = head;
        while (ptr){
            ptr = ptr->next;
            size++;
        }
        ListNode dummy(-1);
        dummy.next = head;
        ListNode* l;        // 左半段
        ListNode* r;        // 右半段
        ListNode* tail;     // 当前链表的尾巴
        for (int i = 1; i < size; i <<= 1){
            // 分割链表 按 1 2 4 8 16...
            ListNode* cur = dummy.next;
            tail = &dummy;
            while (cur){
                l = cur;
                r = split(l, i);
                cur = split(r, i);
                // 合并两个已经分开的链表并且接到tail后面
                tail->next = merge(l, r);
                while (tail->next)  tail = tail->next;
            }
        }
        return dummy.next;
    }

    // 把list分割成前n个 和 后部分 的两个部分
    // 返回的是后部分的头节点
    ListNode* split(ListNode* head, int n){
        // 当只有一个元素的时候直接停止循环了
        while (head && --n)
            head = head->next;
        // 如果head不是nullptr 则选取 head->next 作为开头
        ListNode* rest = head ? head->next : nullptr;
        // 如果head存在 则短链
        if (head)   head->next = nullptr;
        return rest;
    }

    ListNode* merge(ListNode* l1, ListNode* l2) {
        if (!l1)    return l2;
        if (!l2)    return l1;
        ListNode dummy(-1);
        ListNode* ptr = &dummy;
        while (l1 || l2) {
            if (l1 && l2 && l1->val < l2->val) {
                ptr->next = l1;
                l1 = l1->next;
            }
            else if(l2 && l1 && l1->val >= l2->val) {
                ptr->next = l2;
                l2 = l2->next;
            }
            else if (l1) {
                ptr->next = l1;
                l1 = l1->next;
            }
            else {
                ptr->next = l2;
                l2 = l2->next;
            }
            ptr = ptr->next;
        }
        return dummy.next;
    }
};

```

