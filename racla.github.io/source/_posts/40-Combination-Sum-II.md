---
title: 40 Combination Sum II
date: 2019-11-20 13:42:13
tags:
- dfs
categories:
- LeetCode
comments: true
---
# Intuition & Algorithm
I think it's better to compare this question with last question [39 Combination Sum](https://racla.github.io/39-Combination-Sum/). Their difference is:

> One number can appears multiple times, but can only be used once. In last qestion, one number appears once but can be used multiple times.

The constraints of this question is still duplicate combinations is not allowed.Similar to last question, we should focus on how to guarentee unique solution path while using dfs method. Let's see a example below:
> [1,1,1,2,2], target=4

After we add first `1` into our path, can we add the second `1`? The answer is yes, because there is no `1`s between these two `1`s, so another path can't contain other `1`s, there will be no duplicates. But what if the third `1`, can we add it to the path? The answer is no, because there may be another path containing the second `1` and does contain the third `1`, this will cause duplication.  So the crucial part is add `1`s one by one, if previous `1` are not added, we can't add current `1`.

# Code
```
class Solution {
    private List<Integer> path = new LinkedList<Integer>();
    private List<List<Integer>> ans = new LinkedList<>();
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        dfs(candidates, target, 0);
        return ans;
    }
    private void dfs(int[] cand, int target, int start){
        if(target < 0)return;
        if(target ==0 ){
            ans.add(new ArrayList(path));
            return;
        }
        for(int i = start; i < cand.length ;i++){
            if(i > start && cand[i]==cand[i-1])continue;
            if(cand[i] > target)break;
            path.add(cand[i]);
            dfs(cand, target - cand[i], i+1);
            path.remove(path.size()-1);
        }
    }
}
```
# Complexity
**Time Complexity:** $O(n^{target})$

**Space Complexity:** $O(target)$

See detailed explanations [here](https://racla.github.io/39-Combination-Sum/)

