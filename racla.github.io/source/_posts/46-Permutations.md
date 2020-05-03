---
title: 46 Permutations
date: 2019-11-16 00:34:37
categories:
- LeetCode
comments: true
tags:
- backtracking
- dynamic programming
---
# Intuition & Algorithm
It's a dynamic programming question. Suppose we have get the permutations of i number, the `i+1`th number will be added to the previous solution set. For Each permution in the previous solution set, the `i+1`th number can be added to the first place, the second place,... or the last place. These new permutations will be added to the new solution set, and then add `i+2`th number. It's a little sophiscated to describe the state transition equation, so we will go through an example instead.

> Given Numbers: [1,2,3]
> The change of solution set:
> 1. [[1]]
> 2. [[2,1], [1,2]]
> 3. [[3,2,1], [2,3,1], [2,1,3], [3,1,2], [1,3,2], [1,2,3]]

# Code
```
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> ans = new ArrayList<List<Integer>>();
        if(nums.length == 0) return ans;
        List<Integer> temp = new ArrayList<>();
        temp.add(nums[0]);
        ans.add(temp);
        
        for(int i = 1; i < nums.length; i++){
            List<List<Integer>> curAns = new ArrayList<List<Integer>>();
            for(List<Integer> ele: ans){
                for(int j = 0; j <= ele.size(); j++){
                    List<Integer> data = new ArrayList<>();
                    data.addAll(ele);
                    /*
                    for(int k = 0; k< ele.size(); k++){
                        data.add(0);
                    }
                    Collections.copy(data, ele);*/
                    data.add(j, nums[i]);
                    curAns.add(data);
                }
            }
            ans = curAns;
        }
        return ans;
    }
}
```
# Complexity

**Time Complexity:** See the code above, when i = 1, the size of `ans` is 1 and the size of `ele` is 1. When i = n-1, `ans` size is (n-1)! and the size of `ele` is n-1. So the entire time complexity is $T(n)=1*1 + 2 * 2 + ...+ (n-1)!(n-1) $, it's about $O(n!)$

**Space Complexity:** $O(n!)$ for storing all the permutations
