---
title: Leetcode160.相交链表
date: 2022-1-21 9:35:30
---

今天我们来看看一道简单的题，题意[点击跳转](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)，分析之后我们得知，这道题给定两个单链表的头结点`headA`和`headB`，然后要我们找出并返回这两个单链表相交的第一个节点，如果两个链表不存在节点，便返回`null`

我们可以分析下，两个链表，既然要相交，那么相交之前的那段链表必然有长有短或者是同样长度，相交的定义就是这两个单链表的某个值相同那就在这个地方相交，既然要判断值是否相同那就需要一个一个去对比，我们就需要在两个单链表都有值的地方开始遍历去判断值是否相同，所以我们可以有如下思路:

+ 这里有两个链表A和B，headA指向链表A的头节点，headB指向链表B的头结点

+ 初始化两个指针，flagA和flagB，以及链表的长度lenA和lenB
+ 然后求得链表A和B的长度分别为lenA和lenB
+ 求完两个长度之后，两个指针都重新指向链表头部
+ 然后如果A是长链表，则指针flagA后移到和B链表同等长度的位置上
+ 如果B是长链表，则指针flagB后移到和A链表同等长度的位置上
+ 然后两个指针flagA和flagB同时遍历，如果遇到相同的就返回，遍历完没有遇到，就返回None

Python代码实现:

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:

        # 链表A和B任一为空，链表不相交
        if not headA or not headB:
            return None

        # 初始化headA、headB的指针和长度
        flagA, flagB = headA, headB
        lenA, lenB = 0, 0

        # 求链表 A 的长度
        while flagA:
            flagA = flagA.next
            lenA += 1

        # 求链表 B 的长度
        while flagB:
            flagB = flagB.next
            lenB += 1

        # 重新指向表头
        flagA, flagB = headA, headB

        # 为了让大家看的明白点，我就不用华丽花哨的写法了，用最笨的写法表示。
        # 当A是长链表，则指针flagA后移到和B链表同等长度的位置上。
        if lenA > lenB:
            d_value = lenA - lenB
            while d_value:
                flagA = flagA.next
                d_value -= 1
        # 当B是长链表，则指针flagB后移到和A链表同等长度的位置上。
        else:
            d_value = lenB - lenA
            while d_value:
                flagB = flagB.next
                d_value -= 1

        # 然后两个指针flagA和flagB同时遍历
        while flagA:
            if flagA == flagB:
                return flagA
            else:
                flagA = flagA.next
                flagB = flagB.next

        # 如果没有相遇，返回 None
        return None
```



