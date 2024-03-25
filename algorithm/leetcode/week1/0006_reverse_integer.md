# Reverse Integer

[Reverse Integer - LeetCode](https://leetcode.com/problems/reverse-integer/)

## Description

Given a signed 32-bit integer `x`, return `x` *with its digits reversed* . If reversing `x` causes the value to go outside the signed 32-bit integer range `[-2<sup>31</sup>, 2<sup>31</sup> - 1]`, then return `0`.

**Assume the environment does not allow you to store 64-bit integers (signed or unsigned).**

**Example 1:**

<pre><strong>Input:</strong> x = 123
<strong>Output:</strong> 321
</pre>

**Example 2:**

<pre><strong>Input:</strong> x = -123
<strong>Output:</strong> -321
</pre>

**Example 3:**

<pre><strong>Input:</strong> x = 120
<strong>Output:</strong> 21
</pre>

**Constraints:**

* `-2<sup>31</sup> <= x <= 2<sup>31</sup> - 1`

这题不能用 `long long int` 来存。

## Analysis

没啥好说的，用秦九韶算法就可以了。

注意的是，会有负数，你给负数取模的话得到的是负数。

还有一点就是，为什么第二个代码里面 `res > (INT_MAX - x % 10) / 10`，因为 `res + x % 10` 有可能会溢出，会报错。

## Solution

```c++
class Solution {
public:
    int reverse(int x) {
        long long int res = 0;
        while (x) {
            res = res * 10 + x % 10;
            x /= 10;
        }
        if (res > INT_MAX) return 0;
        if (res < INT_MIN) return 0;
        return res;
    }
};
```

```c++
class Solution {
public:
    int reverse(int x) {
        int res = 0;
        while (x) {
            if (res > 0 && res > (INT_MAX - x % 10) / 10) return 0;
            if (res < 0 && res < (INT_MIN - x % 10) / 10) return 0;
            res = res * 10 + x % 10;
            x /= 10;
        }
        return res;
    }
};
```
