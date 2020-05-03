---
title: 19 Remove Nth Node From End of List
date: 2019-11-27 14:35:52
tags:
- list
categories:
- LeetCode
---
# Intuition & Alogorithm
Since it's a singly linked list, we'd can only count the number of the node from head to tail. The equavalent question of this is `remove the (N-n+1)-th node from the head of list and return its head, where N is the length of the list`. There are several corner cases to notice:
1. `n > N or n <= 0` both conditions means we try to remove unexisted nodes in the list(before the head or after the end);
2. `n==N` this means we want to remove the head, we can deal with this one alone, or add a dummy node;

Besides corner cases, we should remember to use fast-slow pointer to remove a node from the list. (I'm used to call them current and previous pointer)

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
    public ListNode removeNthFromEnd(ListNode head, int n) {
        int N = 0;
        ListNode tmp = head;
        while(tmp!=null){
            tmp = tmp.next;
            N++;
        }
        if(n > N || n == 0){
            return head;
        }
        n = N - n;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode cur = head;
        ListNode pre = dummy;
        while(n-- > 0){
            pre = cur;
            cur = cur.next;
        }
        pre.next = cur.next;
        cur = null;
        return dummy.next;
    }
}
```

# Complexity
**Time Complexity:** O(N+n) There are two for loops here, costing O(N) and O(n) respectively.

**Space Complexity:** O(1)
