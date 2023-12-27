---
title: Datawhale数据结构基础课程笔记-(Day3-6)递归
description: 递归（Recursion）：指的是一种通过重复将原问题分解为同类的子问题而解决的方法。在绝大数编程语言中，可以通过在函数中再次调用函数自身的方式来实现递归。
toc: true
authors:
  - Haoran Zhou
tags:
categories:
series:
date: '2023-12-14T13:11:22+08:00'
lastmod: '2023-12-14T13:11:22+08:00'
featuredImage:
draft: false
---

{{< katex >}}

> **递归（Recursion）**：指的是一种通过重复将原问题分解为同类的子问题而解决的方法。在绝大数编程语言中，可以通过在函数中再次调用函数自身的方式来实现递归。

**递归的基本思想**： 把规模大的问题不断分解为子问题来解决。

递归的计算可以分成下面两个过程：
1. 递推过程：先逐层向下调用自身，直到达到结束条件。即将原问题一层一层地分解为与原问题形式相同、规模更小的子问题，直到达到结束条件时停止，此时返回最底层子问题的解；
2. 回归过程：向上逐层返回结果，直到返回原问题的解。即从最底层子问题的解开始，逆向逐一回归，最终达到递推开始时的原问题，返回原问题的解。



参考：[04.02.01 递归算法（第 03 ~ 04 天）](https://github.com/datawhalechina/leetcode-notes/blob/main/docs/ch04/04.02/04.02.01-Recursive-Algorithm.md)

## [509. 斐波那契数](https://leetcode.cn/problems/fibonacci-number/description/)

思路：
1. 题目直接给出了递归函数 $F(n) = F(n - 1) + F(n - 2)$,  
终止条件为 `n=0` 或 `n=1` ，可以容易地写出递归代码：
    ```cpp
    class Solution {
    public:
        int fib(int n) {
            if(n==0){
                return 0;
            }
            else if(n==1){
                return 1;
            }
            return fib(n-1) + fib(n-2);
        }
    };
    ```
    执行用时分布12ms
    消耗内存分布6.23MB

    时间复杂度：$O((\frac{1 + \sqrt{5}}{2})^n)$。
    空间复杂度：$O(n)$。

## [104. 二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/description/)
思路：
1. 我们根据题意建立递归函数： 当前根节点的最大深度 = 叶子节点中的最大深度 + 1，函数表示为
    $$
    maxDepth(root) = max(maxDepth(root\to left), maxDepth(root\to right)) + 1
    $$
    递归的终止条件是当前节点为空（返回0）或者没有叶子节点（返回1）。
    ```cpp
    class Solution {
    public:
        int maxDepth(TreeNode* root) {
            if(root == nullptr){
                return 0;
            }
            if(root->left == nullptr && root->right == nullptr){
                return 1;
            }
            if(root->left == nullptr){
                return maxDepth(root->right) + 1;
            }
            else if(root->right == nullptr){
                return maxDepth(root->left) + 1;
            }
            return max(maxDepth(root->left), maxDepth(root->right)) + 1;
        }
    };
    ```
    执行用时分布12ms
    消耗内存分布18.62MB

    时间复杂度：$O(n)$，其中是二叉树的节点数目。
    空间复杂度：$O(n)$。

## [70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/description/)
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。每次可以爬 1 或 2 个台阶。有多少种不同的方法可以爬到楼顶呢？

思路：
1. 由于第 n 阶楼梯可以是一次爬1个或2个台阶上来的，因此爬 n 阶楼梯的方法数等于爬 n-1 阶楼梯的方法数加上爬 n-2 阶楼梯的方法数，函数表示为
    $$
        climbStairs(n) = climbStairs(n-1) + climbStairs(n-2)
    $$
    终止条件是 n=1， 此时只有一种方法；
    或者 n = 2，此时有两种方法。
    ```cpp
    class Solution {
    public:
        int climbStairs(int n) {
            if(n==1){
                return 1;
            }
            else if(n==2){
                return 2;
            }
            return climbStairs(n-1) + climbStairs(n-2);
        }
    };
    ```
    然后递归的方法会在 n = 44 时超时。
2. 使用动态规划的方法。
    ```cpp
    class Solution {
    public:
        int climbStairs(int n) {
            vector<int> table;
            table.push_back(0);
            table.push_back(1);
            table.push_back(2);

            for(int i = 3; i <= n; ++i){
                table.push_back(table[i-1] + table[i-2]);
            }
            return table[n];
        }
    };
    ```
    执行用时分布0ms
    消耗内存分布6.27MB

## [226. 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/description/)

思路：
1. 可以把翻转二叉树这个问题分解成逐层翻转各结点子树的子问题。我们交换当前节点的左右节点，然后递归这个这节点的左节点，再递归右节点。  
    终止条件是：当前节点是空节点
    ```cpp
    class Solution {
    public:
        TreeNode* invertTree(TreeNode* root) {
            if(root==nullptr){
                return nullptr;
            }
            TreeNode* tmp = root->left;
            root->left = root->right;
            root->right = tmp;
            invertTree(root->left);
            invertTree(root->right);
            return root;
        }
    };
    ```
    执行用时分布0ms
    消耗内存分布9.72MB

## [206. 反转链表](https://leetcode.cn/problems/reverse-linked-list/description/)

思路：
1. 先递推到最后一个节点，令最后的节点指向上一个节点，然后返回上一个节点，作回归。
    终止条件：当前节点或下一个节点为空
    ```cpp
    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            // 这里 head 是当前链表的头
            if(head == nullptr || head->next==nullptr){
                return head;
            }
            //递归传入下一个节点，目的是为了到达最后一个节点
            // 这里返回的是已经翻转后的子链表的表头
            ListNode* new_head = reverseList(head->next);
            // 出栈
            // 我们要让下一个结点指向当前结点，即下下个结点是当前结点
            head->next->next = head;
            // 由于当前结点指向着下一个结点，我们要断开这个链接
            head->next = nullptr;
            return new_head;
        }
    };
    ```
    执行用时分布8ms消耗内存分布8.73MB

## [92. 反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/description/)

思路：
1. 大致思路和上面一样，不过该题中加入了翻转的边界限制。  
    因此我们分成两个步骤来解决，保留前部分链表，以 left 为分割点分割链表，将后面的链表的前 `right-left` 个节点进行翻转操作。  
    步骤一: 递推到第 left 个节点, 然后按顺序回归。
    
    步骤一终止条件：当 `left==1` 时停止，返回处理后的下半部分链表。

    步骤二：对于截断出的链表递推到第 `right-left` 个节点，在回归时进行翻转。
    
    步骤二终止条件：为了定位距离边界 `right` 还有多少个节点，我们在每次递推的时候令 `right--`，当 `right==1` 时停止递推，截断并保存next head，返回当前的 head，向前回归。

    ```cpp
    class Solution {
    private:
        ListNode* last_head = nullptr;
        ListNode* reverseRight(ListNode* head, int right){
            if(right==1){
                last_head = head->next;
                return head;
            }
            ListNode* new_head = reverseRight(head->next, right - 1);
            head->next->next = head;
            head->next = last_head;
            return new_head;
        }

    public:
        ListNode* reverseBetween(ListNode* head, int left, int right) {
            if(left==1){
                return reverseRight(head, right);
            }
            head->next = reverseBetween(head->next, left - 1, right - 1);
            return head;
        }
    };
    ```

## [779. 第K个语法符号](https://leetcode.cn/problems/k-th-symbol-in-grammar/description/)
思路：
1. 第 $i+1$ 行中的第 $x$ 个数字 $\textit{num}_1$，$1 \le x \le 2^i$，会被第 $i$ 行中第 $\lfloor \frac{x + 1}{2} \rfloor$ 个数字 $\textit{num}_2$ 生成。且满足规则：

    当 $\textit{num}_2 = 0$ 时，$\textit{num}_2$ 会生成 $01$：
    $$
    \textit{num}_1 = \begin{cases} 0, & x \equiv 1 \pmod{2} \\ 1, & x \equiv 0 \pmod{2} \\ \end{cases}
    $$
    ( $\equiv$ 表示恒等于，$\pmod{2}$ 表示对2取模)

    当 $num_2 = 1$ 时，$\textit{num}_2$ 会生成 $10$：
    $$
    \textit{num}_1 = \begin{cases} 1, & x \equiv 1 \pmod{2} \\ 0, & x \equiv 0 \pmod{2} \\ \end{cases}
    $$

    并且进一步总结我们可以得到：$\textit{num}_1 = (x \And 1) \oplus 1 \oplus \textit{num}_2$ ，其中 $\And$ 为「与」运算符， $\oplus$ 为「异或」运算符( 两个位相同为0，相异为1 )。那么我们从第 $n$ 不断往上递归求解，并且当在第一行时只有一个数字，直接返回 $0$ 即可。

    ```cpp
    class Solution {
    public:
        int kthGrammar(int n, int k) {
            if(n == 1){
                return 0;
            }
            return (k & 1) ^ 1 ^ kthGrammar(n - 1, (k + 1) / 2);
        }
    };
    ```
    