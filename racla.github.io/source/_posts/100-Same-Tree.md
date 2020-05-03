---
title: 100 Same Tree
date: 2019-11-05 00:10:10
categories:
- LeetCode
tags:
- binary tree
---
## Intuition & Algorithm
The question asks whether the structure of the two binary trees and the values of the nodes are the same. This question has a strong reference significance on how to judge whether two objects are equal.

There are two ideas for the solution. The first one is the recursive method. First, determine whether the root nodes are the same. If they are the same, determine whether the left and right subtrees are the same, which is equivalent to determining whether the root nodes are equal in the depth traversal process. The second idea is to traverse the binary tree in the sequence, store the nodes of the current layer of the two trees in two queues, and determine whether each node is the same in turn. In this process, the left and right child nodes of the node are added to the tail end of the queue. Yes, even if the left and right child nodes are null, you need to join because the two trees may be symmetric structures (see Example 2), and the left and right nodes are not added to the queue until the current node is empty.

This question is almost identical to the [101 symmetry binary tree](https://leetcode.com/problems/symmetric-tree/) idea, except that in the recursive method, it is judged whether the left sub-tree and the right sub-tree are equal, instead of Determine whether the left subtree and the left subtree are equal. Instead of two queues in the sequence traversal, use one queue and one stack.

## Complexity
**Time Complexity:** Time complexity for traversing the entire tree  is O(N)

**Space Complexity:** The recursive method depends on the height of the tree, the worst case is O(N), best case is  O(logN); the number of nodes in the sequence traversal depends on the number of nodes, the worst is O(N), best case is O(1)
> Interestingly, the worst case space complexity in the recursive algorithm is that the binary tree  degenerates to a linked list which resulting in a tree height of O(N), so consumption of function stack is O(N), while the broad fist traversal performs best in this case with space complexity of O(1). The best case of the recursive algorithm is a complete binary tree, in which case the height of the tree is O(logN), when the binary tree is full The number of  last layer nodes is N/2, so the space consumed is O(N).

## Code

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        LinkedList<TreeNode> pl = new LinkedList<>();
        LinkedList<TreeNode> ql = new LinkedList<>();
        
        pl.add(p);
        ql.add(q);
        while(true){
            if(pl.size()==0 && ql.size() == 0){
                return true;
            }
            else if(pl.size()==0 || ql.size() == 0)
            {
                return false;
            }
            //比较队列中的元素，并将下一层结点加入队列
            int np = pl.size();
            int nq = ql.size();
            if(np != nq)return false; //结构不同，一层的节点数量不一样
            //依次比较每个节点
            int i = 0;
            while(i++ < np){
                TreeNode p1 = pl.removeFirst();
                TreeNode q1 = ql.removeFirst();
                if( (p1 == null && q1!=null) || (p1 != null && q1==null) )return false;
                else if(p1!=null && q1!=null && p1.val != q1.val)return false;
                if(p1 != null){
                    pl.add(p1.left);pl.add(p1.right);
                }
                if(q1 != null){
                    ql.add(q1.left);ql.add(q1.right);
                }
            }
        }
    }
}
```


