# Longest Palindromic Substring

[Longest Palindromic Substring - LeetCode](https://leetcode.com/problems/longest-palindromic-substring/description/)

## Description

Given a string `s`, return *the longest palindromic substring* in `s`. （回文串）

**Example 1:**

<pre><strong>Input:</strong> s = "babad"
<strong>Output:</strong> "bab"
<strong>Explanation:</strong> "aba" is also a valid answer.
</pre>

**Example 2:**

<pre><strong>Input:</strong> s = "cbbd"
<strong>Output:</strong> "bb"
</pre>

**Constraints:**

* `1 <= s.length <= 1000`
* `s` consist of only digits and English letters.

## Analysis

效率最高的算法是二分+哈希，但是比较难，这里先不管。

比较简单的算法：

* 枚举一下中心点，从中心向两边开始走，直到走到两个字符不相等或是走到边界为止。
* 对于奇数的情况就是 `l = i - 1` , `r = i + 1`，然后左指针向左走，右指针向右走。
* 对于偶数的请款就是 `l = i` , `r = i + 1`

## Solution

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        string res;
        for (int i = 0; i < s.size(); i ++) {
            int l = i - 1, r = i + 1;
            while (l >= 0 && r < s.size() && s[l] == s[r]) l --, r ++;
            if (res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);
            l = i, r = i + 1;
            while (l >= 0 && r < s.size() && s[l] == s[r]) l --, r ++;
            if (res.size() < r - l - 1) res = s.substr(l + 1, r - l - 1);
        }
        return res;
    }
};
```
