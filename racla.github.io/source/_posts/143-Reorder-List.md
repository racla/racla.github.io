---
title: 143 Reorder List
date: 2019-11-08 00:34:08
categories:
- LeetCode
tags:
- list
- recursion
- pointer
---
# Intuition
From the description of this question, we need three steps to achieve the `reorder` operation for a singly linked list:
1. seperate the list into two parts
2. reverse the right part
3. merge the left part and reversed right part one by one

# Algorithm
###### 1. seperate the list into two parts
A stright and simple method is counting the node number and find the middle one. This will cost ***O(n)*** time.
###### 2. reverse the right part
There are two methods for reversing the list, *recursion* and *pointers*. We are all familiar with the prons and cons of recursion. It's easy to be implemented and understood, but the size of the list are limited by the size of stack frame. The time complexity for reversing a list is still ***O(n)***.
> To be honest, I can't resist the temptation of recursion, it's brief and concise. Sometimes recursion is like a magic, we suppose it works for some case and then deduce the relationship between the past and the future, finally we solved the whole problem, it's always a little unrealistic. We can call this method mathematical induction. Maybe when quantum computer comes out, this method will not be limited by the problem size and can be first choice of different solutions.

###### 3. merge the left part and reversed right part one by one
We just combine two list one by one, for example `1->2->3` and `5->4` will be `1-> 5 ->2-> 4 ->3` after merge. Time complexity is still ***O(n)***.

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
    private ListNode ansHead; 
    public void reorderList(ListNode head) {
        int length = 0;
        ListNode temp = head, newHead = head, pre = head;
        while(temp!=null){
            length++;
            temp= temp.next;
        }
        if(length < 3)return ;
        int mid = (length >> 1)  + 1;
        for(int i = 1; i <= mid; i++){
            pre = newHead;
            newHead = newHead.next;            
        }
        pre.next = null;
        reverse(newHead);
        merge(head, ansHead);
    }
    
    public ListNode reverse(ListNode head){
        if(head.next == null)
        {
            this.ansHead = head;
            return head;
        }
        ListNode next = inverse(head.next);
        next.next = head;
        head.next = null;
        return head;
    }
    /*
    //This function reverse a list by pointers, it cost a constant space
     public ListNode reverse(ListNode head){
        ListNode pre = null, cur = head, next = head;
        while(cur.next != null){
            next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        cur.next = pre;
        ansHead = cur;
        return cur;
    }
    */
    public void merge(ListNode head1, ListNode head2){
        while(head2 != null){
            ListNode next= head2.next;
            head2.next = head1.next;
            head1.next = head2;
            head1 = head2.next;
            head2 = next;
        }
    }

}

```

# Comlexity
***Time Complexity:*** O(n), see analysis above.

***Space Complexity:*** We cost a constant space O(1) if using *pointer* method to reverse a list, other wise it's O(n)

