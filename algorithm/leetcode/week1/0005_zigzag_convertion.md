# Zigzag Convertion

[Zigzag Conversion - LeetCode](https://leetcode.com/problems/zigzag-conversion/description/)

## Description

The string `"PAYPALISHIRING"` is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

```
P   A   H   N
A P L S I I G
Y   I   R
```

And then read line by line: `"PAHNAPLSIIGYIR"`

Write the code that will take a string and make this conversion given a number of rows:

```
string convert(string s, int numRows);
```

**Example 1:**

<pre><strong>Input:</strong> s = "PAYPALISHIRING", numRows = 3
<strong>Output:</strong> "PAHNAPLSIIGYIR"
</pre>

**Example 2:**

<pre><strong>Input:</strong> s = "PAYPALISHIRING", numRows = 4
<strong>Output:</strong> "PINALSIGYAHRPI"
<strong>Explanation:</strong>
P     I    N
A   L S  I G
Y A   H R
P     I
</pre>

**Example 3:**

<pre><strong>Input:</strong> s = "A", numRows = 1
<strong>Output:</strong> "A"
</pre>

**Constraints:**

* `1 <= s.length <= 1000`
* `s` consists of English letters (lower-case and upper-case), `','` and `'.'`.
* `1 <= numRows <= 1000`

## Analysis

首先分析行数为4的情况：（分析器数组下标会如何分布）

```plaintext
0     6      12      18
1   5 7   11 13   17 19
2 4   8 10   14 16   20
3     9      15      21
```

可以看到第一行是一个 `2n - 2` 的一个等差数列，最后一行也是一个 `2n - 2` 的一个等差数列。

然后再看第二行和第三行，第二行可以拆成两个等差数列分别是 `1 7 13 19` 和 `5 11 17 19`，也是两个公差为 `2n - 2` 的一个等差数列。

然后思考等差数列的起点：

* 数线上的起点很好思考就是从 `0` 到 `n-1`，
* 那么斜线上的等差数列应该如何思考呢？
  * 可以找规律 `1+5=6` `2+4=6`
  * 所以斜线上等差数列的起点便是 `2n - 2 - i`，`i` 为行标

然后呢，我们需要特判一下 `n == 1` 的情况，因为很容易发现，当 `n == 1` 的时候公差等于$0$了，程序便陷入了死循环。但是当 `n == 1` 的时候，就还是原来的序列，直接返回一下 `s` 就可以了。

## Solution

```c++
class Solution {
public:
    string convert(string s, int n) {
        if (n == 1) return s;
        string res;
        for (int i = 0; i < n; i ++) {
            if (i == 0 || i == n - 1)
                for (int j = i; j < s.size(); j += 2 * n - 2)
                    res += s[j];
            else
                for (int j = i, k = 2 * n - 2 - i; j < s.size() || k < s.size(); j += 2 * n - 2, k += 2 * n - 2) {
                    if (j < s.size()) res += s[j];
                    if (k < s.size()) res += s[k];
                }
        }

        return res;
    }
};
```
