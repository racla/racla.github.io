---
title: 99 Recover Binary Search Tree
date: 2019-11-05 00:08:35
categories:
- LeetCode
comments: true
tags:
- binary tree
---
## Intuition & Algorithm
According to the location of the two nodes being exchanged, it can be divided into five cases, assuming that the original sequence is 1，2，3，4，5，6，7，the two node locations exchanged arei, j

1. The two elements of the exchange are in the middle of the sequence, without the situation on both sides, say 2,6,  then
    * Before Exchange   `a[i-1] < a[i] < a[i+1]` `a[j-1] < a[j] < a[j+1]`
    * After Exchange  &nbsp; `a[i-1] < a[i] > a[i+1]` `a[j-1] > a[j] < a[j+1]`
    * Different Part &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;` a[i] > a[i+1]` `a[j-1] > a[j]`
2. The smaller element is in the first place, the larger element is in the middle of the sequence, such as 1,5, then
    * Before Exchange   `a[i] < a[i+1]` `a[j-1] < a[j] < a[j+1]`
    * After Exchange   &nbsp; `a[i] > a[i+1]` `a[j-1] > a[j] < a[j+1]`
    * Different Part &nbsp;&nbsp;&nbsp;&nbsp; ` a[i] > a[i+1]` `a[j-1] > a[j]`
3. The smaller element is in the middle of the sequence, the larger element is at the end, such as 3, 7, then
    * Before Exchange   `a[i-1] < a[i] < a[i+1]` `a[j-1] < a[j]`
    * After Exchange &nbsp;  `a[i-1] < a[i] > a[i+1]` `a[j-1] > a[j]`
    * Different Part &nbsp;&nbsp;&nbsp;&nbsp; ` a[i] > a[i+1]` &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;`a[j-1] > a[j]`
4. The smaller elements are at the top of the sequence, and the larger ones are at the end, ie 1,7,  then:
    * Before Exchange   ` a[i] < a[i+1]` `a[j-1] < a[j]`
    * After Exchange   ` a[i] > a[i+1]` `a[j-1] > a[j]`
    * Different Part &nbsp;&nbsp;&nbsp; ` a[i] > a[i+1]` `a[j-1] > a[j]`
5. adjacent elements， say 3 and 7， then
    * Before Exchange   ` a[i] < a[i+1]` 
    * After Exchange   ` a[i] > a[i+1]` 
    * Different Part&nbsp;&nbsp;&nbsp; ` a[i] > a[i+1]` 

Therefore, by detecting the anomaly, the two elements exchanged can be found. The anomaly can be summarized as one case: ` a[k] > a[k+1]`. In the case of an exception, the kth point and k+1th point can both be the exchange position, depending on the number of occurrence of the anomaly. If it is the first time an exception occurs, it must be that the larger element is changed to the front, so k is the abnormal point. If it is the second time, it must be that the  smaller element is changed to the back, so k+1 is the abnormal point, and there is an adjacent case, that is, the exception only appears once, and can be assumed by the **The two positions of the secondary anomaly are all abnormal points, and if a second exception occurs, the smaller abnormal points are overlayed.**.

The Java code is as follows, it should be noted that
- The value of the two nodes is directly exchanged in the code. It is also very simple to exchange two nodes.
- `A solution using O(n) space is pretty straight forward.` this narrative should refer to `using an array with length n to store the binary tree after the traversal, and then find the location of the two exceptions.` This algorithm is easy to implement, and the solution in the constant space should be referred to use constant space except traversal. Or think so, to find the two exchanged points we  must traverse over all the nodes, so the consumption of traversing the tree is inevitable.

# Complexity
**Time complexity: ** Detecting anomalies requires traversing all nodes, so it is O(N)

**Spatial complexity** See the analysis in the previous section. Strictly consider the consumption of the stack in recursion is O(logN)
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
    TreeNode pre, small, large;
    public void recoverTree(TreeNode root) {
        inTraverse(root);
        int v1 = small.val;
        small.val = large.val;
        large.val = v1;
    }
    
    void inTraverse(TreeNode root){
        if(root == null)return;
        inTraverse(root.left);
        if(pre == null){// 第一个节点
            pre  = root;
        }
        else{
            if(root.val < pre.val){
                if(large == null){
                    large = pre;
                    small = root;
                }
                else
                    small = root;
            }
            pre = root;
        }
        inTraverse(root.right);
    }
    
}
```

