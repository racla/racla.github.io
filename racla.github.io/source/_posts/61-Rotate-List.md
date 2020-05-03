---
title: 61 Rotate List
date: 2019-11-23 21:25:36
tags:
- list
categories:
- LeetCode
---
# Intuition & Algorithm
Remove the last k nodes before the head node.
# Code
```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if(head ==null)return head;
        int n = 0;
        ListNode tail = null;
        for(ListNode i = head; i!=null;n++,tail=i,i=i.next);
        k=k%n;
        if(k==0)return head;
        ListNode pre=null;
        ListNode c=head;
        for(int i = 0 ; i < n-k; i++,pre=c,c=c.next);
        tail.next = head;
        head = pre.next;
        pre.next=null;
        return head;
    }
}
```

# Complexity
**Time Complexity:** There are two for loop here, cost $O(n)$ time to traverse the lsit.
**Space Complexity:** $O(1)$
