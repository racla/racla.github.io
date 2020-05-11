---
title: 55 Jump Game
date: 2019-11-05 00:10:24
categories:
- LeetCode
tags:
- dynamic programming
---
# Intuition & Algorithm
It's a typical dynamic programming problem, so we can find three kinds of solution.
- Recursion
- Memoization (Optimization of Recursion)
- Bottom-up (Iteration)

Each element in the array represents the maximum length we can jump and we need to find out if we can reach the last element starting from the first element.

###### 1. Recursion
The broute-force solution is  recursion and backtracking. Let's assum the length of the given array `nums[i]` is `n`, and we want to check if we can jump to the last element from position `i`, this process can be represent as following equation:

```
S(i) = S(i+1) || ... || S(m), m = min(i+k, n), k=nums[i]
```

So the problem is seperated into several sub-problems and the terminated case is `S(n-1)=true` since we just stand on the last element. 

***Time complexity*** is O(2^n) because we can choose skip each position or not in each possible solution. ***Space comlexcity*** is O(n) consumed by function stack frame.

###### 2. Memoization
The problem of recursion is the solution of each subproblem is imutable, but we calculate it multiple times. Say, `S(i) = S(i+1) || S(i+2) || S(i+3)` and `S(i-1) = S(i+1) || S(i+2) `， `S(i+2)` is repeatedly calculated. So we can store each solution `S(i)`, when we need it just return it. For each `i`, we solved `nums[i]` problems, and `nums[i]` is at most `n`, so time complexity is `O(n^2)`. Space comlexity is O(2n) since we need O(n) for stack frame and O(n) for memo table.

###### 3. Bottom-up
The top-down recusion process of above solution is redundant. We can first get `S(n-2)` with the precondition `S(n-1) = true` and then get `S(n-3)`, `S(n-2)`...until `S(0)`. This saves the space consumption for stack frame in recursion. Even more, the scale of this problem is not limited by recusion. For instance, the length of the array is 100000, so we need 100000 stack frame in the worst case. Operating system has limited the size of stack frame. Recursion will cause the `stack overflow` error. 

The implementation of bottom-up solution is quite easy too , we can use an array to store the result of the subproblems and update the element from the last to the first. S(0) is the final answer. Time complexity is still O(n^2) and space complexity is O(n).

###### 4. Greedy(Best)
In the above solution, we are finding the nearst position that can jump to the last. An clear illustration can be got from the above equation:
`S(i) = S(i+1) || ... || S(m)` Logic operation `||` will terminated when an operand is true from left to right, that's exactly the **nearst** jumpable position. The neast jumpable position `njp` is `n-1`, we update it as searching from `n-2` to `0`. While at position `i`, if `i+nums[i] < njp`, we can't jump to the nearst jumpable position so we can't jump to the end, otherwise we set position `i` to jumpable and `njp` to `i`.
Time complexity is O(n) and Space complexity is O(1). This is the best solution for ***Jump Game*** problem.
# Complexity
Look up the details in the above section.

###### 1. Recursion
**Time Complexity:**  O(2^n)

**Space Complexity:**  O(n)

###### 2. Memoization
**Time Complexity:**  O(n^2)

**Space Complexity:**  O(n)

###### 3. Bottom-up
**Time Complexity:**  O(n^2)

**Space Complexity:**  O(n)

###### 4. Greedy(Best)
**Time Complexity:**  O(n)

**Space Complexity:**  O(1)

# Code
###### 1. Recursion
```
public class Solution {
    public boolean canJumpFromPosition(int position, int[] nums) {
        if (position == nums.length - 1) {
            return true;
        }

        int furthestJump = Math.min(position + nums[position], nums.length - 1);
        for (int nextPosition = position + 1; nextPosition <= furthestJump; nextPosition++) {
            if (canJumpFromPosition(nextPosition, nums)) {
                return true;
            }
        }

        return false;
    }

    public boolean canJump(int[] nums) {
        return canJumpFromPosition(0, nums);
    }
}
```

###### 2. Memoization
```
/**
* Official Solution: GOOD denotes jumpable position, BAD is not jumpable
*/
enum Index {
    GOOD, BAD, UNKNOWN
}

public class Solution {
    Index[] memo;

    public boolean canJumpFromPosition(int position, int[] nums) {
        if (memo[position] != Index.UNKNOWN) {
            return memo[position] == Index.GOOD ? true : false;
        }

        int furthestJump = Math.min(position + nums[position], nums.length - 1);
        for (int nextPosition = position + 1; nextPosition <= furthestJump; nextPosition++) {
            if (canJumpFromPosition(nextPosition, nums)) {
                memo[position] = Index.GOOD;
                return true;
            }
        }

        memo[position] = Index.BAD;
        return false;
    }

    public boolean canJump(int[] nums) {
        memo = new Index[nums.length];
        for (int i = 0; i < memo.length; i++) {
            memo[i] = Index.UNKNOWN;
        }
        memo[memo.length - 1] = Index.GOOD;
        return canJumpFromPosition(0, nums);
    }
}
```

###### 3. Bottom-up
```
class Solution {
    public boolean canJump(int[] nums) {
        boolean[] dp = new boolean[nums.length];
        dp[nums.length - 1] = true;
        for(int i = nums.length - 2; i >= 0; i--){
            int k = nums[i];
            dp[i] = false;
            int mb = Math.min(k, nums.length-i-1);
            for(int j = 1 ; j <= mb; j++){
                if(dp[i+j])
                {
                    dp[i] = true;break;
                }
            }
        }
        return dp[0];
    }
}
```

###### 4. Greedy(Best)
```
class Solution {
    public boolean canJump(int[] nums) {
        int njp = nums.length -1;
        for(int i = nums.length -2 ;  i >=0; i--){
            if(nums[i] + i >= njp){
                njp = i;
            }
        }
        return njp == 0; 
    }
}
```

# Reference
[What Is Dynamic Programming and How To Use It](https://www.youtube.com/watch?v=vYquumk4nWw&feature=youtu.be)

[Official Solution](https://leetcode.com/problems/jump-game/solution/)
