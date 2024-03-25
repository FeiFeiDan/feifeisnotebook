# Regular Expression Matching

[Regular Expression Matching - LeetCode](https://leetcode.com/problems/regular-expression-matching/description/)

## Description

Given an input string `s` and a pattern `p`, implement regular expression matching with support for `'.'` and `'*'` where:

* `'.'` Matches any single character.
* `'*'` Matches zero or more of the preceding element.

The matching should cover the **entire** input string (not partial).

**Example 1:**

<pre><strong>Input:</strong> s = "aa", p = "a"
<strong>Output:</strong> false
<strong>Explanation:</strong> "a" does not match the entire string "aa".
</pre>

**Example 2:**

<pre><strong>Input:</strong> s = "aa", p = "a*"
<strong>Output:</strong> true
<strong>Explanation:</strong> '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
</pre>

**Example 3:**

<pre><strong>Input:</strong> s = "ab", p = ".*"
<strong>Output:</strong> true
<strong>Explanation:</strong> ".*" means "zero or more (*) of any character (.)".
</pre>

**Constraints:**

* `1 <= s.length <= 20`
* `1 <= p.length <= 20`
* `s` contains only lowercase English letters.
* `p` contains only lowercase English letters, `'.'`, and `'*'`.
* It is guaranteed for each appearance of the character `'*'`, there will be a previous valid character to match.

## Analysis

### 闫氏 DP 分析法

动态规划：

* 状态表示 $f(i, j)$
  * 集合：所有 `s[1~i]` 和 `p[1~j]` 的匹配方案
  * 属性：是否存在一个合法方案
* 状态计算
  * 如果 `p[j] == '*'` 的话，就直接看 `s[i]` 和 `p[j]` 是否匹配就可以了
    * `s[i]` 和 `p[j]` 匹配有两种情况，一种是它俩直接匹配 `s[i] == p[j]`，第二种是  `p[j] == '.'`，而且他们的前一个字符也要匹配 `f(i-1, j-1)`
  * 如果 `p[j] == '*'` 的话，我们就要枚举一下这个 `'*'` 表示多少个字符
    * 首先判断一下表示$0$个字符的情况：表示$0$个字符的话就表示 `a*` 这两个字符是多余的，所以说比较$f(i, j -2)$就可以了
    * 如果是表示$1$个字符的话：那么就$f(i-1, j-2)$，并且 `s[i] == p[j]`
    * 如果是表示$2$个字符的话：那么就是$f(i-2, j-2)$，并且 ……
* 优化之后就变成了：`f(i, j) = f(i, j-2) || f(i-1, j) && s[i] == p[j]`

## Solution

```c++
class Solution {
public:
	bool isMatch(string s, string p) {
		int n = s.size(), m = p.size();
		// 因为是从 1 开始，所以先在 s 和 p 前面补一个空格
		s = ' ' + s, p = ' ' + p;
		// 然后定义状态转移函数
		vector<vector<bool>> f(n + 1, vector<bool>(m + 1));
		f[0][0] = true;
		for (int i = 0; i <= n; i ++)
			for (int j = 1; j <= m; j ++) {
				if (j + 1 <= m && p[j + 1] == '*') continue;
				if (i && p[j) != '*') {
					f[i][j] = f[i - 1][j - 1] && (s[i] == p[j] || p[j] == '.');
				} else if (p[j] == '*') {
					f[i][j] = f[i][j - 2] || i && f[i - 1][j] && (s[i] == p[j - 1] || p[j - 1]=='.');
				}
			}
		return f[n][m];
	}
};
```
