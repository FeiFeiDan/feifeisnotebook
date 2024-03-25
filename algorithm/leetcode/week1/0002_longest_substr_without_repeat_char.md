# Longest Substring Without Repeating Characters

最长不重复字符子串

[Longest Substring Without Repeating Characters - LeetCode](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)

## Description

**Example 1:**

<pre><strong>Input:</strong> s = "abcabcbb"
<strong>Output:</strong> 3
<strong>Explanation:</strong> The answer is "abc", with the length of 3.
</pre>

**Example 2:**

<pre><strong>Input:</strong> s = "bbbbb"
<strong>Output:</strong> 1
<strong>Explanation:</strong> The answer is "b", with the length of 1.
</pre>

**Example 3:**

<pre><strong>Input:</strong> s = "pwwkew"
<strong>Output:</strong> 3
<strong>Explanation:</strong> The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
</pre>

**Constraints:**

* `0 <= s.length <= 5 * 10<sup>4</sup>`
* `s` consists of English letters, digits, symbols and spaces.

## Analysis

这是一个双指针问题，这种问题背后的原则是：两个指针只会从左到右滑动。

* `i` 在右边，`j` 在左边。
  * 如果 `i` 向右边滑动导致区间内不满足要求（在这个问题下是区间里面有重复字符），为了让这个区间满足要求，`j` 一定是要向右滑动的
  * 所以不管是 `i` 还是 `j` 都只用向右滑动就可以了（好马不吃回头草），所以是 $2n$ 次操作，所以时间复杂度仍然是 $O(n)$ 的
  * 然后用一个哈希表来维护 $[j, i]$ 这个区间就好了

## Solution

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int res = 0;
        unordered_map<char, int> heap;
        for (int i = 0, j = 0; i < s.size(); i ++) {
            heap[s[i]] ++;
            while (heap[s[i]] > 1) {
                -- heap[s[j ++]];
            }
            res = max(res, i - j + 1);
        }
        return res;
    }
};
```
