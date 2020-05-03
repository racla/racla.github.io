---
title: 23 Merge k Sorted Lists
date: 2019-11-27 14:36:20
tags:
- list
categories:
- LeetCode
---
# Intuition 
This seems to be a general question of merging two sorted lists. The naive method of this question is quite intuitive. If you are interested in more efficient solutions, skip to `Partition Algorithm` and `Heap Algorithm` directly. Let's look at the example below to illustrate our naive method.
>Input: <br>
[<br>
  1->4->5,<br>
  1->3->4,<br>
  2->6<br>
]<br>
Output: 1->1->2->3->4->4->5->6<br>

Let's start with a `dummy` node to represent our final list. The next node of `dummy` should be the minimum one from three headers: 1,1,2, it's the first header or the second one, either is ok, let's take the first one in this case. And we should move the header of the first list to its next one. 

>lists: <br>
[<br>
  4->5,<br>
  1->3->4,<br>
  2->6<br>
]<br>
ans: -1(dummy)->1<br>

Then we pick the minimum node from lists into `ans` list again. It's the head node from second list.
>lists: <br>
[<br>
  4->5,<br>
  3->4,<br>
  2->6<br>
]<br>
ans: -1(dummy)->1->1<br>

We do this `N` times in total. We compare `k` headers each time. where `k` is the number of lists to be merged. So time complexity is $O(Nk)$ in this method.

# Heap Algorithm
Where is the bottleneck of naive method? We have to pick `N` nodes, so the time complexity must be $O(N*X)$. Can we reduce X from `k` to something smaller than it? The answer is yes. Review the process when we pick a node, we pick it from k nodes, and then change one of the k nodes, other k-1 nodes remains the same. In this circumstance
, heap is a perfect data structure. The query complexity and insert complexity for a small root queue are $O(logk)$ instead of $O(k)$ in the naive method.
# Partition Algorithm
Don't forget it's a merge sortion question although this is for lists. Recall that how do we merge sorted arrays, every two of them are merged into one sorted array, and then we merge the new arrays with half number of previous one. Finally, we merge two sorted array. Actually, this is the same case for lists. Each time we merge every two of them, they will be merged into one after $log(k)$ times. So the time complexity is $O(Nlogk)$.  
# Code
1. Heap Algorithm
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
    public ListNode mergeKLists(ListNode[] lists) {
        PriorityQueue<ListNode> heap = new PriorityQueue<>(new Comparator<ListNode>(){
            public int compare(ListNode n1, ListNode n2){
                if(n1.val < n2.val){
                    return -1;
                }
                else if(n1.val == n2.val){
                    return 0;
                }
                else
                    return 1;
            }}
        );
        for(ListNode n : lists){
            if(n != null)
                heap.add(n);
        }
        ListNode dummy = new ListNode(-1), cur = dummy;
        while(heap.size() > 0){
            cur.next = heap.poll();
            cur = cur.next;
            if(cur.next != null){
                heap.add(cur.next);
            }
        }
        return dummy.next;
    }
}

```
2. Partition Algorithm
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
    public ListNode mergeKLists(ListNode[] lists) {
        if(lists.length == 0)return null;
        List<ListNode> pool = new ArrayList<ListNode>(Arrays.asList(lists));
        while(pool.size() > 1){
            pool = merge(pool);
        }
        return pool.get(0);
    }
    private List<ListNode> merge(List<ListNode> pool){
        List<ListNode> res = new ArrayList<ListNode>();
        for(int i = 0; i< pool.size(); i+=2){
            if(i == pool.size()-1){
                res.add(pool.get(i));
                break;
            }
            res.add(mergeTwoLists(pool.get(i), pool.get(i+1)));
        }
        return res;
    }
    private ListNode mergeTwoLists(ListNode list1, ListNode list2){
        if(list1 == null) return list2;
        if(list2 == null) return list1;
        ListNode dummy = new ListNode(0), cur = dummy;
        while(list1 != null && list2 != null){
            if(list1.val < list2.val)
            {
                cur.next = list1;
                list1 = list1.next;
            }
            else{
                cur.next = list2;
                list2 = list2.next;
            }
            cur = cur.next;
        }
        if(list1 == null){
            cur.next = list2;
        }
        else{
            cur.next = list1;
        }
        return dummy.next;
    }
}
```
# Complexity
**Time Complexity:** $O(Nlog(k))$ See explanations in Algorithm sections.

**Space Complexity:** $O(k)$ for the heap
