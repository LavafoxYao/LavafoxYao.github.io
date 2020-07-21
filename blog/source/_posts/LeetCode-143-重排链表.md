---
title: LeetCode 143.重排链表
date: 2020-07-21 13:20:42
tags: ['LeetCode', '链表']
categories: 题解
---

#### [143. 重排链表](https://leetcode-cn.com/problems/reorder-list/)

<!--more-->

> 给定一个单链表 L：L0→L1→…→Ln-1→Ln ，
> 将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…
>
> 你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。
>
>  **示例 1:** 
>
> ```
> 给定链表 1->2->3->4, 重新排列为 1->4->2->3.
> ```
>
>  **示例 2:** 
>
> ```
> 给定链表 1->2->3->4->5, 重新排列为 1->5->2->4->3.
> ```

思想:

1. 先用快慢指针把 链表划分为 前后两部分(按道理 如果是奇数 后面长度大于前面
2. 再把后部分的链表逆置
3. 最后逐步插入

```C++
class Solution {
public:
    void reorderList(ListNode* head) {
        if (!head)  return;
        ListNode* slow = head, * fast = head;
        while (fast->next && fast->next->next){
            slow = slow->next;
            fast = fast->next->next;
        }
        ListNode* l = head, *r = slow->next;
        slow->next = nullptr;    // 断开前后链表
        ListNode* pre = nullptr, *ptr = r;
        while (ptr){
            ListNode* tmp = ptr->next;
            ptr->next = pre;
            pre = ptr;
            ptr = tmp;
        }
        r = pre;
        while (l && r){
            ListNode* l_next = l->next;
            ListNode* r_next = r->next;
            l->next = r;
            l = l_next;
            r->next = l;
            r = r_next;
        }
    }
};
```

