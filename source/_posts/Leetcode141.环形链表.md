---
title: Leetcode141.环形链表
date: 2022-1-21 11:00:30
---

我们来看下LeetCode上一个相对简单的题，[环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)，这里给定一个链表，要求我们判断链表中是否有环

这道题的思路就是快慢指针，快慢指针这种算法证明，一快一慢两个指针肯定是会相遇的，快的指针一定会追上慢的指针，可以理解为操场上跑步，跑得快的人会超圈追上跑得慢的人，一般使用fast定义快指针，用slow定义慢指针，速度不同是指fast每次多走几步，slow少走几步，一般的设定都是fast走2步，slow走1步；本题就是使用快慢指针

+ 若是链表**无环**，那么fast指针回先指向Null
+ 若是链表**有环**，fast和slow迟早会在环中相遇

Python代码实现:

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: ListNode) -> bool:

        # 空链表或链表只有一个节点，无环
        if not head or head.next == None:
            return False

        # 初始化快慢指针
        fast = slow = head

        # 如果不存在环，肯定 fast 先指向 null
        # 细节：fast 每次走 2 步，所以要确定 fast 和 fast.next 不为空，不然会报执行出错。
        while fast and fast.next:

            # 快指针移动 2 步，慢指针移动 1 步
            fast = fast.next.next
            slow = slow.next

            # 快慢指针相遇，有环
            if fast == slow:
                return True

        return False
```

