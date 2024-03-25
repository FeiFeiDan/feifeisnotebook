# Parlindrome Number

[Palindrome Number - LeetCode](https://leetcode.com/problems/palindrome-number/)

## Description

Given an integer `x`, return `true`*if *`x`* is a **palindrome** *, and *`false`* otherwise* .

**Example 1:**

<pre><strong>Input:</strong> x = 121
<strong>Output:</strong> true
<strong>Explanation:</strong> 121 reads as 121 from left to right and from right to left.
</pre>

**Example 2:**

<pre><strong>Input:</strong> x = -121
<strong>Output:</strong> false
<strong>Explanation:</strong> From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
</pre>

**Example 3:**

<pre><strong>Input:</strong> x = 10
<strong>Output:</strong> false
<strong>Explanation:</strong> Reads 01 from right to left. Therefore it is not a palindrome.
</pre>

**Constraints:**

* `-2<sup>31</sup> <= x <= 2<sup>31</sup> - 1`

**Follow up:** Could you solve it without converting the integer to a string?

## Analysis

就是判断一下回文数，没有什么好说的。不过这题倒是可以记一下 C++ 库函数的使用。

## Solution

### 先转换为字符串，然后将字符串翻转

```c++
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0) return false;
        string s = to_string(x);
        return s == string(s.rbegin(), s.rend()); // r begin, r end, 反向迭代器
    }
};
```

### 用之前写过的整数反转算法

之所以要用 `long long int` 的原因是，虽然输入的数是 `int` 类型的，但是翻转了之后就不一定还在 `int` 范围内了。

```c++
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0) return false;
        int y = x;
        long long int r = 0;
        for (; x; x /= 10) {
            r = r * 10 + x % 10;
        }
        return r == y;
    }
};
```
