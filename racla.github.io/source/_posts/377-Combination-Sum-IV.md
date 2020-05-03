---
title: 377 Combination Sum IV
date: 2019-11-20 13:42:42
tags:
- dynamic programming
categories:
- LeetCode
comments: true
---
# Intuition & Algorithm
At the first glampse of this question, I write down the dfs algorithm immediately. It works of course, but will encounter the `Time Exceed` error. The problem here is 
it costs too much time to search all the possible paths, there are duplicates. For example:
> [1, 2, 3] target=6

when we have picked 1 and 2, we need to find a combination summing to 3. When we have picked 3, again we need to find a combination whose sum is 3. So we do this subproblem multiple times
> [1,2,3] target=3

It reminds us that dynamic programming can be used here. The state transition equation is:

$$dp[target] = dp[target-nums[0]]+dp[target-nums[1]]+...+dp[target-nums[n]]$$


# Code
```
class Solution {

    public int combinationSum4(int[] nums, int target) {
        Arrays.sort(nums);
        if(nums.length == 0 || nums[0] > target)
            return 0;
        int[] dp = new int[target + 1];
        dp[nums[0]] = 1;
        dp[0] = 1;
        for(int i = nums[0]+1; i <= target;i++){
            for(int j = 0; j < nums.length; j++){
                if(i < nums[j])break;
                dp[i] += dp[i-nums[j]];
            } 
        }
        return dp[target];
    }
   
}
```

# Complexity
**Time Complexity:** $O(target*n)$ Outter for loop iterates from nums[0] to target and inner for loop iterates over the array.

**Space Complexity:** $O(target)$ The cost of `dp` arrray. 
