---
title: 148 Sort List
date: 2019-11-10 20:54:24
categories:
- LeetCode
tags:
- list
- pointer
---
# Intuition & Algorithm
The diagram and illustration in the question have given us a clear description of insertion sort algorithm.
1. Separate the list into two parts: `sorted` and `sorting`
2. Insert the head of `sorting` list into the sorted list

We should consider three cases during insertion(v is the node to be inserted):
- `v < sorted.head.val` we should use this node as the new head of sorted list
- `cur.val < v < cur.next.val` `cur` is a node in the sorted list, in this case we should insert the new node between `cur` and `cur.next`
- `cur.next == null` this means `v` is large than any number in the list, so we append it to the tail.


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
    public ListNode insertionSortList(ListNode head) {
        if(head == null)return head;
        ListNode sorted=head,sorting = head.next, next;
        head.next = null;
        while(sorting != null){
            next = sorting.next;
            
            ListNode cur = sorted;
            if(sorting.val < cur.val){
                sorting.next = sorted;
                sorted = sorting;
            }
            else{
                while(cur.next != null){
                    if(sorting.val >= cur.val && sorting.val <= cur.next.val)
                    {
                        sorting.next = cur.next;
                        cur.next = sorting;
                        break;
                    }
                    cur = cur.next;
                }
                if(cur.next == null){
                    sorting.next = null;
                    cur.next = sorting;
                }
            }
            sorting = next;
        }
        return sorted;
    }
}
```

# Complexity
**Time Complexity:** $O(n)$

**Spatial Complexity:** $O(1)$
