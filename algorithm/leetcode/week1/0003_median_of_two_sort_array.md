# Median of Two Sort Array

[Median of Two Sorted Arrays - LeetCode](https://leetcode.com/problems/median-of-two-sorted-arrays/description/)

## Description

Given two sorted arrays `nums1` and `nums2` of size `m` and `n` respectively, return **the median** of the two sorted arrays.

The overall run time complexity should be `O(log (m+n))`.

**Example 1:**

<pre><strong>Input:</strong> nums1 = [1,3], nums2 = [2]
<strong>Output:</strong> 2.00000
<strong>Explanation:</strong> merged array = [1,2,3] and median is 2.
</pre>

**Example 2:**

<pre><strong>Input:</strong> nums1 = [1,2], nums2 = [3,4]
<strong>Output:</strong> 2.50000
<strong>Explanation:</strong> merged array = [1,2,3,4] and median is (2 + 3) / 2 = 2.5.
</pre>

**Constraints:**

* `nums1.length == m`
* `nums2.length == n`
* `0 <= m <= 1000`
* `0 <= n <= 1000`
* `1 <= m + n <= 2000`
* `-10<sup>6</sup> <= nums1[i], nums2[i] <= 10<sup>6</sup>`

## Analysis

这道题首先想到的是一个 $O(n)$级别的算法，就是将两个数组先合并，然后找到中位数。但是这道题要求$O(log(n))$级别的做法。

首先，可以将问题转化为求第 $k$ 小的数。

如果该问题可以得到解决的话，那么第$(n+m)/2$小的数就是我们要求的中位数。

对于寻找第 $k$ 小的数，首先分别在 `A B` 两个数组里一起找第 $\frac{k}{2}$ 个位置。（先不考虑$k$的奇偶问题）

1. 如果$A[\frac k2] < B[\frac k2]$ 的话，那么小于等于$A[\frac k2]$ 的个数一定是小于$k$的。
   1. 那么在数组$A$中，小于等于$A[\frac k2]$的元素就可以删掉。
2. 反之，如果$A[\frac k2] > B[\frac k2]$ 的话，那么数组$B$中小于等于$B[\frac k2]$的元素就可以删掉
3. 如果$A[\frac k2] = B[\frac k2]$ 的话，那么这两个值就恰好是等于第$k$个元素。

那么每次比较完这两个值之后，一定可以删掉$\frac k2$个元素。所以这个做法最多递归$log(k)$次。（当$k$等于1时，相当于找两个数组里的最小值。）

所以说这道题的代码思路就是：

* 首先求出这两个数组一共有多少个元素 `tot`
  * 如果 `tot` 的值为偶数的话
    * 就先找第$\frac{tot}{2}$小的数，然后找第$\frac{tot}{2}+1$小的数，然后返回这两个数的平均（要除以$2.0$，不然的话会取整）。
  * 如果s `tot` 是基数的话，直接返回这个数就可以了。
* 然后实现寻找第$k$小的数的函数
  * 假定第一个数组比第二个数组短，如果第一个数组比第二个数组长的话，就交换这两个数组
  * 然后处理一下边界问题
    * 当$k$等于$1$的时候（只剩下一个数了）取这两个数组的最小值就可以了
      * 继续判断边界条件
        * 如果第一个数组是空的话，就只能返回第二个数组的第一个元素
        * 然后又因为第一个数组的长度一定小于第二个数组的长度，所以如果有一个数组是空的话，那么一定是第一个数组
      * 如果没有数组是空的话，就返回这两个数组第一个元素的最小值
    * 如果第一个数组是空的话，那么返回第二个数组第$k$小个元素就好了（返回第$j+k-1$个数，因为$k$是从$1$开始的）
  * 一般情况
    * 判断这两个数组第$\frac k2$个位置的大小，然后将较小的那个数组小于的那部分删掉。（考虑奇偶的话一个是$\frac k2$，一个是$k-\frac k2$）
    * 然后递归判断
    * 注意：短数组是有可能越界的

### 这道题能学到的思路

1. 不同问题之间的转换
2. 递归问题的处理经验（边界的设定）
3. 第几个数和数组下标之间的对应关系

## Solution

```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int tot = nums1.size() + nums2.size();
        if (tot % 2 == 0) {
            int left = find(nums1, 0, nums2, 0, tot/2);
            int right = find(nums1, 0, nums2, 0, tot/2 + 1);
            return (left + right) / 2.0;
        } else {
            return find(nums1, 0, nums2, 0, tot/2 + 1);
        }
    }
    int find(vector<int>& nums1, int i, vector<int>& nums2, int j, int k) {
        if (nums1.size() - i > nums2.size() - j) return find(nums2, j, nums1, i, k);
        if (k == 1) {
            if (nums1.size() == i) return nums2[j];
            else return min(nums1[i], nums2[j]);
        }
        if (nums1.size() == i) return nums2[j + k - 1];
        int si = min((int)nums1.size(), i + k/2), sj = j + k - k/2;
        if (nums1[si - 1] > nums2[sj - 1]) {
            return find(nums1, i, nums2, sj, k - (sj - j));
        } else {
            return find(nums1, si, nums2, j, k - (si - i));
        }
    }
};
```
