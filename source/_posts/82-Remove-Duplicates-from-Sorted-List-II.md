---
title: 82 Remove Duplicates from Sorted List II
date: 2019-11-27 14:36:08
tags:
- list
categories:
- LeetCode
---
# Intuition
There are several stuff to notice:
1. Remove the duplicates, keep no one of them
2. Find the duplicates by three pointers: pre,cur and next, indicating previous node current node and next node. When the value of next node and cur node are equal, move next node to the one that not equal to cur node, and connect it to pre node, so the duplicates are removed.

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
    public ListNode deleteDuplicates(ListNode head) {
        ListNode cur = head;
        ListNode dummy = new ListNode(0), pre = dummy;
        dummy.next = head;
        while(cur != null){
            ListNode next = cur.next;
            while(next!=null && next.val == cur.val){
                next = next.next;
            }
            if(cur.next == next){
                pre.next = cur;
                cur = cur.next;
                pre = pre.next;
            }
            else
            {
                pre.next = next;
                cur = next;
            }
            
        }
        return dummy.next;
    }
}
```
# Complexity
**Time Complexity:** $O(N)$ We use three pointers to iterate over the list.

**Space Complexity:** $O(1)$
