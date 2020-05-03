---
title: 120  Triangle
date: 2019-11-08 23:49:20
categories:
- LeetCode
tags:
- dynamic programming
---
# Intuition
The **broute-force solution** for this method is to list all the possible path, and then get the minimum.
In the top layer of the triangle, we can only choose index *0*. In the second layer, we can choose *0* or *1* for *0* in the first layer. In the third layer, we can choose *0* or *1* for *0* in second layer and *1* or *2* for *1*... these choices form a binary tree. Each path from root to leaf node is related to a path from top to bottom in the trianlgle.So the time complexity is $O(2^n)$.

From the above analysis, we find out this is a **dynamic programming** problem, so we can use **memoization** to store the minimum sum from top to the entry in the previous layer and then compare the entries in current layer and get the minimum. For example, in the **i**th layer, for the **j**th element, we have:
```
memo[i][j] = Math.min(memo[i-1][j-1], memo[i-1][j])+triangle[i][j]
```
The equation above actually has also give us the state transition formula for this question. What we want is `min(memo[n-1][j])` with j from 0 to n-1. The question has an extra bonus for less space.`Bonus point if you are able to do this using only O(n) extra space, where n is the total number of rows in the triangle.` Currently, space complexity is $O(n^2)$, but we don't need to save all the middle result in array since current state is only relative to previous one, not all the past state. So we can use two one dimensional array, one for previous state, and one for current state. If we look deeper into the state transition equation, one array is enough, we just update elements in previous array to current array one by one.

> Note: *j-1* might be less than 0 and *j* might be large than i(in the ith layer, the maximum index is i). We need to deal with these two cases.

# Algorithm
As stated before, this is a dynamic problem, there are three methods:
1. Recursion
2. Memoization (Optimize for recursion)
3. Iteration

We will only talk  about the iteration method here. Since we have given the two dimensional state  transition equation before, one dimensional state transition equation will be given here, which should be the best solution for this question. Note j is from i to 0 in case we shltered the old state before we don't need it.

*State Transition:* 

$$
dp[j] = min(dp[j-1],dp[j])+triangle[i][j], j\in{i, i-1, ..., 0} \quad i\in0,1,...,n
$$

# Code 
```
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {

        int n = triangle.size();
        int[] dp = new int[n];
        dp[0] = triangle.get(0).get(0);
        for(int i = 1; i < n; i++){
            for(int j = i; j >= 0; j--){
                int l = Math.max(0, j-1);
                int r = Math.min(i-1, j);
                dp[j] = Math.min(dp[l], dp[r]) + triangle.get(i).get(j);
            }
        }
        int ans = Integer.MAX_VALUE;
        for(int i = 0; i < n; i++){
            ans = Math.min(ans, dp[i]);
        }
        return ans;
    }
}
```
# Complexity
**Time Complexity:** $O(n^2)$

**Space Complexity:** $O(n)$

