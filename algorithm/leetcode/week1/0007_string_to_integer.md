# String to Integer

[String to Integer (atoi) - LeetCode](https://leetcode.com/problems/string-to-integer-atoi/description/)

## Description

Implement the `myAtoi(string s)` function, which converts a string to a 32-bit signed integer (similar to C/C++'s `atoi` function).

The algorithm for `myAtoi(string s)` is as follows:

1. Read in and ignore any leading whitespace.
2. Check if the next character (if not already at the end of the string) is `'-'` or `'+'`. Read this character in if it is either. This determines if the final result is negative or positive respectively. Assume the result is positive if neither is present.
3. Read in next the characters until the next non-digit character or the end of the input is reached. The rest of the string is ignored.
4. Convert these digits into an integer (i.e. `"123" -> 123`, `"0032" -> 32`). If no digits were read, then the integer is `0`. Change the sign as necessary (from step 2).
5. If the integer is out of the 32-bit signed integer range `[-2<sup>31</sup>, 2<sup>31</sup> - 1]`, then clamp the integer so that it remains in the range. Specifically, integers less than `-2<sup>31</sup>` should be clamped to `-2<sup>31</sup>`, and integers greater than `2<sup>31</sup> - 1` should be clamped to `2<sup>31</sup> - 1`.
6. Return the integer as the final result.

**Note:**

* Only the space character `' '` is considered a whitespace character.
* **Do not ignore** any characters other than the leading whitespace or the rest of the string after the digits.

**Example 1:**

<pre><strong>Input:</strong> s = "42"
<strong>Output:</strong> 42
<strong>Explanation:</strong> The underlined characters are what is read in, the caret is the current reader position.
Step 1: "42" (no characters read because there is no leading whitespace)
         ^
Step 2: "42" (no characters read because there is neither a '-' nor '+')
         ^
Step 3: "<u>42</u>" ("42" is read in)
           ^
The parsed integer is 42.
Since 42 is in the range [-2<sup>31</sup>, 2<sup>31</sup> - 1], the final result is 42.
</pre>

**Example 2:**

<pre><strong>Input:</strong> s = "   -42"
<strong>Output:</strong> -42
<strong>Explanation:</strong>
Step 1: "<u></u>-42" (leading whitespace is read and ignored)
            ^
Step 2: "   <u>-</u>42" ('-' is read, so the result should be negative)
             ^
Step 3: "   -<u>42</u>" ("42" is read in)
               ^
The parsed integer is -42.
Since -42 is in the range [-2<sup>31</sup>, 2<sup>31</sup> - 1], the final result is -42.
</pre>

**Example 3:**

<pre><strong>Input:</strong> s = "4193 with words"
<strong>Output:</strong> 4193
<strong>Explanation:</strong>
Step 1: "4193 with words" (no characters read because there is no leading whitespace)
         ^
Step 2: "4193 with words" (no characters read because there is neither a '-' nor '+')
         ^
Step 3: "<u>4193</u> with words" ("4193" is read in; reading stops because the next character is a non-digit)
             ^
The parsed integer is 4193.
Since 4193 is in the range [-2<sup>31</sup>, 2<sup>31</sup> - 1], the final result is 4193.
</pre>

**Constraints:**

* `0 <= s.length <= 200`
* `s` consists of English letters (lower-case and upper-case), digits (`0-9`), `' '`, `'+'`, `'-'`, and `'.'`.

## Analysis

去除一下前导 `0`，然后再判断一下正负就可以了。

然后仍然要注意数据溢出的问题。

## Solution

```c++
class Solution {
public:
    int myAtoi(string s) {
        int k = 0;
        for (; k < s.size() && s[k] == ' '; k ++) ;
        if (k == s.size()) return 0;

        int minus = 1;
        if (s[k] == '-') minus = -1, k ++;
        else if (s[k] == '+') k ++;

        int res = 0;
        for (; k < s.size() && s[k] <= '9' && s[k] >= '0'; k ++) {
            if (res > 0 && res > (INT_MAX - minus*(s[k] - '0')) / 10) return INT_MAX;
            if (res < 0 && res < (INT_MIN - minus*(s[k] - '0')) / 10) return INT_MIN;
            res = res * 10 + minus*(s[k] - '0');
        }

        return res;
    }
};
```
