---
title: 216 Combination Sum III
date: 2019-11-20 13:42:31
tags:
- dfs
categories:
- LeetCode
comments: true
---
# Intuition & Algorithm
Numbers 1-9 can be seen as candidates in the previous questions, the constraints of this question are:
1. We can only pick one number one time, but we can only pick `k` numbers.
2. All numbers are unique

Actually,  this question is much easier than before since numbers are unique and we can only pick one number one time. If we pick these numbers by ascending order, there will be no duplicates. We only need to record the depth of the path in dfs, ensuring it no more than `k`.


# Code
```
class Solution {
    List<Integer> path = new LinkedList<>();
    List<List<Integer>> ans = new LinkedList<>();
    public List<List<Integer>> combinationSum3(int k, int n) {
        if(k > 9 || n <= 0)
            return ans;
        dfs(k, n, 1);
        return ans;
    }
    private void dfs(int k, int n , int c){
        if(k==0 && n==0){
            ans.add(new ArrayList<Integer>(path));
            return;
        }
        if(k==0 || n<=0 || c>9)
        {
            return;
        }
        for(int i = c; i < 10; i++){
            path.add(i);
            dfs(k-1, n-i, i+1);
            path.remove(path.size()-1);
        }
    }
}
```

# Complexity
**Time Complexity:** $O(9^k)$

**Space Complexity:** $O(km)$ where m is the number of solutions, it's a math problem and I'm not sure what it is. 
