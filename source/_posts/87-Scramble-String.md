---
title: 87 Scramble String
date: 2019-11-06 18:34:07
categories:
- LeetCode
comments: true
tags:
- dynamic programming
---
# Intuition
This is a ***dynamic programming*** problem. Given two string `S1(1,2,3,...,n)` and `S2(1,2,3,...,n)`, they are scramble if and only if:
1. `S1(1,2,3,...,i) and S2(1,2,3,...,i) are scramble` and `S1( i+1,...,n) and S2(i+1,...,n) are scramble`
> For `abb` and `bab`, they can be seperated into two parts: `ab` and `ba` are scramble , `b` and `b`
are scramble.
2. `S1(1,2,3,...,i) and S2(n-i+1,n-i+2,...,n) are scramble` and `S1( i+1,...,n) and S2(1,2,...,n-i) are scamble`
> In the above cases, `ab` and `ba` are scramble because they can be seperated into two parts, `a` and `b`, `b` and `a`, the left part of the first string equals the right part of the second string, the right part of the left equals the left part of the second, they are symmetric.

The termination condition for this recursion solution is two strings are equal.

# Algorithm
###### Recusion

*Termination Condition:* If two strings are equal, then they are also scramble.

*Recursion Process:* For `S1(1,2,3,...,n)`,  `S2(1,2,3,...,n)` and a specified substring size `i` ranging from 1 to n, we need to split the strings into two parts, and check if the substrings are scramble according to the illustration stated before.

*Trick:* We can do a precheck before recusion process, it's much less time consumping than recusion. We can store how many times each character appears in both strings, if they are not equal, these two strings can never be scamble. Say `afbihuifafaf` and `ghuiigafsafh`, if we donot have this precheck process, we have to compare:

> `a` and `g`, `fbihuifafaf` and `huiigafsafh` <br/>
> `a` and `h`, `fbihuifafaf` and `ghuiigafsaf`<br/>
>`af` and `gh`, `bihuifafaf` and `uiigafsafh` <br/>
> ...

That's a huge workload!
###### Iteration

*State Transition:* dp[n][n][n+1] is state matrix, dp[i][j][k] denotes if substring `S1(i, i+1, ..., i+k-1)` and `S2(j, j+1, ..., j+k-1)` are scramble. So dp[0][0][n] is our final answer. According to above analysis, state transition equation should be:
```
dp[i][j][k] = (dp[i][j][p] && dp[i+p][j+p][k-p])||(dp[i][j+k-p][p] && dp[i+p][j][k-p]) p={1,2,...,k-1}
```

# Code
###### Recusion
```
class Solution {
    public boolean isScramble(String s1, String s2) {
        if(s1.equals(s2))
            return true;
        if(s1.length() == 1 && !s1.equals(s2))
            return false;
        int[] letters = new int[26];
        for (int i=0; i<s1.length(); i++) {
            letters[s1.charAt(i)-'a']++;
            letters[s2.charAt(i)-'a']--;
        }
        for (int i=0; i<26; i++) if (letters[i]!=0) return false;
        
        for(int i = 1; i < s1.length(); i++){
            String s1_left = s1.substring(0, i);
            String s2_left = s2.substring(0, i);
            String s1_right = s1.substring(i);
            String s2_right = s2.substring(i);
            if(isScramble(s1_left, s2_left) && 
                isScramble(s1_right, s2_right))return true;
            if(isScramble(s1.substring(0, i), s2.substring(s2.length()-i, s2.length())) && 
                isScramble(s1.substring(i, s1.length()), s2.substring(0, s1.length()-i)))return true;
        }
        return false;
    }
}
```

###### Iteration
```
class Solution {
    public boolean isScramble(String s1, String s2) {
        if(s1.equals(s2))return true; //include corner case:  "" and "" 
        int n = s1.length(); // the question statement has clarified s1.length() == s2.length()
        boolean[][][] dp = new boolean[n][n][n+1];
        for(int k = 1; k <= n; k++){
            for(int i = 0; i <= n-k; i++){
                for(int j = 0; j <= n - k; j++){
                    if(k == 1){
                        dp[i][j][k] = s1.substring(i, i+1).equals(s2.substring(j, j+1));
                    }
                    else{
                        dp[i][j][k] = false;
                        for(int p = 1 ; p < k; p++){
                            if( (dp[i][j][p]&&dp[i+p][j+p][k-p]) || (dp[i][j+k-p][p]&&dp[i+p][j][k-p]))
                            {
                                dp[i][j][k] = true;
                                break;
                            }
                        }
                    }
                }
            }
        }
        return dp[0][0][n];
    }
}
```
# Complexity

###### Recursion

***Time Complexity:*** O(2^n)

***Space Complexity:** *O(2^n), See the blog [here](http://n00tc0d3r.blogspot.com/2013/05/scramble-string.html)

###### Iteration
***Time Comlexity:*** For the iteration solution, it's easy to figure out the approximate time complexity, since we have four for loop, it costs O(n^4). The accurate analysis is more complicated, you can refer to discussions [here](https://leetcode.com/problems/scramble-string/discuss/29387/Accepted-Java-solution) or the [blog](http://n00tc0d3r.blogspot.com/2013/05/scramble-string.html).

***Space Complexity:*** O(n^3), costs for the array`dp[n][n][n+1]`
