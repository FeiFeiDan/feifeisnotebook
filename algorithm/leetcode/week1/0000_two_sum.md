# Two Sum

[Two Sum - LeetCode](https://leetcode.com/problems/two-sum/)

## Description

Given an array of integers `nums` and an integer `target`, return  *indices of the two numbers such that they add up to `target`* .

You may assume that each input would have  ***exactly* one solution** , and you may not use the *same* element twice.

You can return the answer in any order.

**Example 1:**

<pre><strong>Input:</strong> nums = [2,7,11,15], target = 9
<strong>Output:</strong> [0,1]
<strong>Explanation:</strong> Because nums[0] + nums[1] == 9, we return [0, 1].
</pre>

**Example 2:**

<pre><strong>Input:</strong> nums = [3,2,4], target = 6
<strong>Output:</strong> [1,2]
</pre>

**Example 3:**

<pre><strong>Input:</strong> nums = [3,3], target = 6
<strong>Output:</strong> [0,1]
</pre>

**Constraints:**

* `2 <= nums.length <= 10<sup>4</sup>`
* `-10<sup>9</sup> <= nums[i] <= 10<sup>9</sup>`
* `-10<sup>9</sup> <= target <= 10<sup>9</sup>`
* **Only one valid answer exists.**

**Follow Up:**  Can you come up with an algorithm that is less than $O(n^2)$ time complexity?

## Analysis

### 暴力算法

就是用一个双重for循环，时间复杂度为 $O(n^2)$。

### 优化算法 1

使用哈希表，遍历两遍，第一遍将所有数存入到一个哈希表中。第二遍遍历的时候，再在哈希表里找两数之和。

### 优化算法 2

其实不用遍历两边，第一遍遍历的时候就可以一边存放，一边查询。

## Solution

使用 `unordered_map`，这个数据结构使用哈希表实现的。

### Code

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        // 首先创建一个键值对，这里使用的是unordered_map，这个数据结构是用哈希表实现的
        unordered_map<int, int> heap;
        for (int i = 0; i < nums.size(); i ++) {
            int r = target - nums[i];
            if (heap.count(r)) return {heap[r], i};
            else heap[nums[i]] = i;
        }
        return {};
    }
};
```
