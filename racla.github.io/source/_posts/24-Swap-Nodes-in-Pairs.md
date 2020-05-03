---
title: 24 Swap Nodes in Pairs
date: 2019-11-23 21:25:15
tags:
- list
categories:
- LeetCode
---
# Intuition & Algorithm
>Question 25 is a more general question of this one.

Suppose there are three pairs $p_0$, $p_1$ and $p_2$. We need to swap nodes in $p_1$, after that, we should reconnect it to it's adjacent pairs by:

$$
p_0(1).next=p_1(0)
p_1(1).next=p_2(0)
$$

For the first pair, $p_0(1)=null$ so we don't need the first equation and and for the last one, $p_2(0)=null$;
# Code
1. Recursion
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
    public ListNode swapPairs(ListNode head) {
        return dp(head);
    }
    
    private ListNode dp(ListNode head){
        if(head == null || head.next == null)
            return head;
        ListNode next = head.next.next;
        head.next.next = head;
        head = head.next;
        head.next.next = dp(next);
        return head;
    }
}
```

2.Iteration
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
    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null)
            return head;
        ListNode gH = head;
        ListNode gT = head.next;
        head = gT;
        while(gH != null && gT != null){
            ListNode tmp = gT.next;
            gT.next = gH;
            if(tmp != null && tmp.next != null){
                gH.next = tmp.next;
                gH = tmp; gT = tmp.next;
            }
            else{
                gH.next = tmp;
                gH = tmp; gT = null;
            }
        }
        return head;
    }
}
```

# Complexity
**Time Complexity:** $O(n)$ becasue we iterate over the entire list

**Space Complexity:** For the recursion solution, $O(\lceil n/2 \rceil)$ space is necessary to store all the pairs. We cost constant space $O(1)$ for the iteration method.
