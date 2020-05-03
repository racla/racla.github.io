---
title: 39 Combination Sum
date: 2019-11-19 00:08:36
tags:
- dfs
- dynamic programming
categories:
- LeetCode
comments: true
---
# Intuition && Algorithm
It's `dfs`+`dynamic programming` problem. To get all the possible combination adding up to the target, we need to solve several subproblems. Before all, let's think about when will there be no solution set for this problem. The naive idea is to sort the array, if the first number is less than target there will be no solution.

Let's look at an example and see how to solve it.
> Input: candidates = [2,3,6,7], target = 7, 
<br/>A solution set is:
[
  [7],
  [2,2,3]
]

First, suppose there is a `2` in one possible solution(one path if considering dfs), and then we need to solve a combination sum of target `5`(7-2=5) and the same candidates problem. When the target is equal to one candidate, we find a solution(path). But if this target is smaller than the first value, then this is not a possible solution. We can use a formula to describe this solution.

$$S(target)=S(target-candidate[0])+S(target-candidate[1])+...+S(target-candidate[n])$$

$S(i)$ denotes a solution set, it can be empty.

By far, this is a typic process for solving `dfs`+`dynamic programming` problem. But we ignored one import thing, the solutions in solution set can't be duplicated. For the example above, $S(7)=S(5)+S(4)+S(1)+[7]$, and $S(5)=[2,3]$ $S(4)=[2,2]$, both of them give the same solution indeed $[2,2,3]=[3,2,2]$. Duplicated solution occurs because we can choose value in any sequence. If we are only allowed to choose them in non-decreasing order, duplicates can be avoided. In this case only $[2,2,3]$ is allowed.
# Code
```
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Arrays.sort(candidates);
        return findSolutionSet(candidates, target, 0);
    }
    private List<List<Integer>> findSolutionSet(int[] candidates, int target, int pre){
        List<List<Integer>> ans = new ArrayList<>();
        // terminate condition
        if(target < candidates[0])
            return ans;
        for(int i = 0; i < candidates.length; i++){           
            int cur = candidates[i];
            if(cur < target && cur >= pre){
                List<List<Integer>> preRes = findSolutionSet(candidates, target - cur, cur);
                if(preRes.size() > 0){
                    for(List<Integer> r: preRes){
                        r.add(cur);
                        ans.add(r);
                    }
                }
            }
            else if(cur == target && cur >= pre){
                List<Integer> t = new ArrayList<>();
                t.add(cur);
                ans.add(t);

            }
        }
        return ans;
    }
}
```

# Complexity

**Time Complexity:** Time Complexity for sorting an array is $O(nlogn)$ and for dynamic programming portion it's about $O(n^{target})$. You can get the approximate time complexity from the equation for solving this problem above, all the possible solutions construct a tree. 

**Space Complexity:** From the equation above, we can simply think it as $O(target)$ since the solution set has `target` member in the worst case.
