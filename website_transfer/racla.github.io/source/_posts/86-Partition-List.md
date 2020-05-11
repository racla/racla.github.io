---
title: 86 Partition List
date: 2019-11-27 14:36:36
tags:
- list
categories:
- LeetCode
---
# Intuition
This question is one step of quick sort: split the list into two parts by a sentinal. We need three pointers to deal with the classifying process:
1. One pointer denotes the right border of the smaller part, at first it's at the dummy node before head node;
2. The other two pointers are for one goal: iterate over the entire list, move node into the smaller part or remain at its original place up to the given sentinal `x`.
> Keep in mind we need two pointers to remove a node. The critial idea of this algorithm is to  remove a node from the list and then insert it the the right border of the smaller part(after the slow pointer).

# Algorithm
Let's look at an example below:
> Input: head = 1->4->3->2->5->2, x = 3 <br/>
Output: 1->2->2->4->3->5

The three pointers stated above are denoted by`s`(slow), `f`(fast), `p`(pre, node before fast pointer).

1. Initial State

> -1(s,p)->1(f)->4->3->2->5->2 <br/>

2. Check the first node

Since `1 < 3`, we should remove fast node into smaller part, and then move fast node to next node. Here is a corner case, when fast node is right the next node of slow node, we don't need to remove it from the list.
> -1->1(s,p)->4(f)->3->2->5->2 <br/>

3. Check the second node

`4>3`, so we don't move any node, just move fast pointer and pre pointer to its next place
> -1->1(s)->4(p)->3(f)->2->5->2 <br/>

4. Check the third node

The same case as the second one
> -1->1(s)->4->3(p)->2(f)->5->2 <br/>

5. Check the fourth node

`2<3`, we need to remove it from the list and insert it after slow pointer and then move two pointers to its next node. Take care that we don't need to move pre pointer in this case.
> -1->1->2(s)->4->3(p)->5(f)->2 <br/>

6. Check the nodes left

We will get the list finally(`null` node after the tail node is ignored in previous diagram):
> -1->1->2->2(s)->4->3->5(p)->null(f) <br/>

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
    public ListNode partition(ListNode head, int x) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode fast = head, slow = dummy, pre = dummy;
        while(fast != null){
            if(fast.val < x){
                ListNode next = fast.next;
                if(fast != slow.next){
                    fast.next = slow.next;
                    pre.next = next;
                }
                slow.next = fast;
                slow = fast;
                fast = next;
            }
            else{
                pre = fast;
                fast = fast.next;
            }
        }
        return dummy.next;
    }
}
```

# Complexity
**Time Complexity:** $O(n)$ It's the cost of iterating over a list.

**Space Complexity:** $O(1)$ Constant pointers are used.
