---
title: 47 Permutations II
date: 2019-11-16 00:34:52
categories:
- LeetCode
comments: true
tags:
- backtracking
- dynamic programming
- review
---
# Intuition & Algorithm
Recall how we get permutations for numbers without duplication: for each permutation in last solution set, we insert the new number to all possible places. We won't have any duplicated new permutation in this case. But if we insert a number that have been in the permutation, we may get duplicated new permutation. Here are two example:

`[2,2,1]` For this permutation, we want to insert value 1 in the first, second until the last place, that will give us new permutations: [1,2,2,1], [2,1,2,1], [2,2,1,1] [2,2,1,1]. Notice the last two permutation, we get two duplicated permutation because we try to insert a value before and after another value, and they are equal, so we get equal permutation. In this case, we try to insert 1 before 1 and after 1, both of them will get `1,1`. So we can use a flag to record the last value we inserted before, if it's equal to the value we want to insert, we shouldn't insert the value after it.

`[2,1,1], [1,2,1]` In this case, we want to insert value 2 into two permutations. If we insert 2 into the third place of the first permutation, this will give us `[2,1,2,1]`. If we insert 2 into the first place of the second permutation, we still get `[2,1,2,1]`. The insertion of the second permutation shouldn't start from the first place, actually it should start before the last position of 2 instead. The reason here is if we insert before the last 2, and then we remove last 2 from the permutation, we will get an old permutation that already exist. In this case, we insert 2 in the first place , we get `[2,1,2,1]`, and then we remove last 2, it becomes `[2,1,1]`! This means we can get `[2,1,2,1]` from `[2,1,1]` by insert 2 between two 1s.So compared to the normal permutation problem, we should start from the last index of the value to be inserted instead of 0.
# Code
```
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        if(nums.length == 0) return ans;
        List<Integer> t = new ArrayList<>();
        t.add(nums[0]);
        ans.add(t);
        for(int i = 1 ; i < nums.length; i++){
            List<List<Integer>> curAns = new ArrayList<>();
            for(List<Integer> ele : ans){
                int flag = Integer.MIN_VALUE;
                int k = Math.max(0, ele.lastIndexOf(nums[i]));
                for(int j = k; j <= i; j++){
                    if(nums[i] != flag){
                        List<Integer> copy = new ArrayList<>();
                        copy.addAll(ele);
                        copy.add(j, nums[i]);
                        curAns.add(copy);
                    }
                    if(j != i)
                    flag = ele.get(j);
                }
            }
            ans = curAns;
        }
        return ans;
    }
}
```
# Complexity
**Time Complexity:** $O(n!)$

**Space Complexity:** $O(n!)$

See the analysis in `46 permutation`, they are similar, we only add two conditions in this solution.

