---
title: 49 Group Anagrams
date: 2019-11-16 00:34:23
categories:
- LeetCode
comments: true
tags:
- HashMap
---
# Intuition & Algorithm
The primitive problem of this question is how to define anagrams. The definition from wikipedia is:
> An anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

So if two strings have the same letters neglecting their appearing sequences, they are anagrams. The typical solution is using an array with length 26( since there are only `a-z` in this question) to count how many times each letter has appeared in this word. The time complexity of this question is $O(mn)$ where m is the average length of each string, n is the number of original strings. This is an approximate time complexity, and you can get the accurate by the code below.

Some would like to sort the string(don't forget it's a char sequence) to determine if two strings are anagrams, but that will change the time complexity to $o(mlogm*n)$. Another thing to notice is that you can determine two equal array by `Arrays.toString` method, but `Arrays.hashCode` will work faster, see the details in the discussion of this question.

There is still one thing we are worry about: will `Arrays.hashCode` arise collision? Here is the implementation of it:
```
     *
     * <p>The value returned by this method is the same value that would be
     * obtained by invoking the {@link List#hashCode() <tt>hashCode</tt>}
     * method on a {@link List} containing a sequence of {@link Integer}
     * instances representing the elements of <tt>a</tt> in the same order.
     * If <tt>a</tt> is <tt>null</tt>, this method returns 0.
     *
     * @param a the array whose hash value to compute
     * @return a content-based hash code for <tt>a</tt>
     * @since 1.5
     */
    public static int hashCode(int a[]) {
        if (a == null)
            return 0;

        int result = 1;
        for (int element : a)
            result = 31 * result + element;

        return result;
    }
```

Suppose the final count array is $a_1, a_2, ..., a_26$, the hashCode we get is:

$$31*(31*(31 * 1 + a_1) + a_2)+...=1*31^{27} + a_1*31^{26}+a_2*31^{25}+...+a_{26}*31^{1}$$

If all elements in the array is less than 31 and the total sum is less than `Integer.MAX_VALUE`, there will be no collision . We can create a example that will cause collision easily:

`["abbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb", "aa"]`

In this case, $a_1=1,a_2=31$ and $a_1=2, a_2 = 0$ would get the same hashCode, so these two strings will be classified as anagrams although they are not in fact.

# Code
```
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        HashMap<Integer, List<String>> map = new HashMap<>();
        for(int i = 0; i < strs.length; i++){
            Integer key = encode(strs[i]);
            List<String> entry = map.getOrDefault(key, new ArrayList<>());
            entry.add(strs[i]);
            map.put(key, entry);
        }
        return new ArrayList<>(map.values());
    }
    private static int encode(String input){
        int[] sta = new int[26];
        for(Character c: input.toCharArray()){
            sta[c-97]++;
        }
        /*We can use hashCode here, but don't forget the collision case*/
        return Arrays.toString(sta);
    }
}
```
# Complexity
**Time Complexity:** $O(mn)$

**Space Complexity:** $O(mn)$ it's the size of the hashmap storing final answer.
