# Add Two Numbers

[Add Two Numbers - LeetCode](https://leetcode.com/problems/add-two-numbers/description/)

## Description

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in  **reverse order** , and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/10/02/addtwonumber1.jpg)

<pre><strong>Input:</strong> l1 = [2,4,3], l2 = [5,6,4]
<strong>Output:</strong> [7,0,8]
<strong>Explanation:</strong> 342 + 465 = 807.
</pre>

**Example 2:**

<pre><strong>Input:</strong> l1 = [0], l2 = [0]
<strong>Output:</strong> [0]
</pre>

**Example 3:**

<pre><strong>Input:</strong> l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
<strong>Output:</strong> [8,9,9,9,0,0,0,1]
</pre>

**Constraints:**

* The number of nodes in each linked list is in the range `[1, 100]`.
* `0 <= Node.val <= 9`
* It is guaranteed that the list represents a number that does not have leading zeros.

## Analysis

这是一道模拟题，用链表来模拟小学时学的竖式加法，*但是难点是两个列表的长度不相同*。

小技巧1：创建一个虚拟头节点，这样的插入的时候就不用判断第一个点是不是头节点了。凡是需要特判第一个点的时候，我们都可以加入一个虚拟头节点。

* 首先创建一个虚拟头节点，然后让一个节点 `cur` 指向这个虚拟头节点
* 然后开启一个 `while` 循环，只要 `l1` `l2` `t`，中有一个不为 `0`，就继续这个循环
  * 在循环内部：如果 `l1` 不是空节点的话，`t += l1->value; l1 = l1->next`，对于 `l2` 来说的话类似
  * 然后创建一个新的节点，`val` 初始值为  `t % 10`，然后让 `cur->next` 指向这个节点
  * `t /= 10`

## Solution

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int t = 0;
        auto dummy = new ListNode(-1), cur = dummy;
        while (l1 || l2 || t) {
            if (l1) t += l1->val, l1 = l1->next;
            if (l2) t += l2->val, l2 = l2->next;
            cur = cur->next = new ListNode(t%10);
            t /= 10;
        }
        return dummy->next;
    }
};
```
