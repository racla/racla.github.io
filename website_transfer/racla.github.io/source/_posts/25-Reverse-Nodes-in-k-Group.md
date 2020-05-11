---
title: 25 Reverse Nodes in k-Group
date: 2019-11-23 21:25:25
tags:
- list
categories:
- LeetCode
---
# Intuition & Algorithm
The very intuitive method to solve this problem is:
1. splice the List into k nodes a group
2. reverse the group list
3. reconnect groups

A simple implementation is recursion, first reverse the last group, return its head, and then reverse the group before the last one, set its tail to last group's head and return current group head. The whole process goes until the first group is reversed and its tail is set, head is the final answer. The iteration version of this method is using a stack to store each group's head, the algorithm goes similar.

Another algorithm not so straight-forward I call it forward iteration. Because we don't reverse the group from the last one to the first one, we reverse the first group and record it's tail and next group's head, and then connect them together.
# Code
1. Forward Iteration 
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
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        int n = 0;
        ListNode c = head;
        while(c!=null){
            n++;c=c.next;
        }
        ListNode pre = dummy, tail = head;
        while(n>=k){a
            for(int i = 1 ; i < k; i++){
                ListNode next = tail.next.next;
                tail.next.next = pre.next;
                pre.next = tail.next;
                tail.next = next;
            }
            pre = tail;
            tail = tail.next;
            n-=k;
        }
        return dummy.next;
    }
}
```
2. Backward Recursion
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
   public ListNode reverseKGroup(ListNode head, int k) {
    ListNode curr = head;
    int count = 0;
    while (curr != null && count != k) {
        curr = curr.next;
        count++;
    }
    if (count == k) { 
        curr = reverseKGroup(curr, k); 
        while (count-- > 0) { 
            ListNode tmp = head.next; 
            head.next = curr;  
            curr = head; 
            head = tmp; 
        }
        head = curr;
    }
    return head;
}
}
```
3. Backward Iteration
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
    public ListNode reverseKGroup(ListNode head, int k) {
        // If less than k nodes, return directly
        if(k<=1)return head;
        List<ListNode> stack = new LinkedList<>();
        int count = 0;
        ListNode cur = head, lastHead = null;
        int start = 0;
        while(cur != null){
            count++;
            if(count % k == 1)stack.add(cur);
            cur = cur.next;
        }
        start = stack.size()-1;
        if(count < k)return head;
        if(count % k != 0)
        {
            lastHead = stack.get(start--);
        }
        for(int i = start; i>=0; i--){
            lastHead = reverseGroup(stack.get(i), lastHead, k);
        }
        return lastHead;
    }
    private ListNode reverseGroup(ListNode head, ListNode lastHead, int k){
        ListNode cur = head, next = head.next;
        while(k-- > 0){
            cur.next = lastHead;
            lastHead = cur;
            cur = next;
            if(cur != null)
            next = cur.next;
        }
        return lastHead;
    }
    
}
```

# Complexity

**Time Complexity:** $O(n)$ All the solutions are linear since we have to traverse over the entire list.

**Space Complexity:** For the forward iteration solution it's $O(1)$, for the other two, we need $O(\lceil n/k \rceil)$ to store the head node of all groups.

