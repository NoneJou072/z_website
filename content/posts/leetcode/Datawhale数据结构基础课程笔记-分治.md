<!-- ---
title: Datawhale数据结构基础课程笔记-(Day3-6)分治
description: 分治算法（Divide and Conquer）：字面上的解释是「分而治之」，就是把一个复杂的问题分成两个或更多的相同或相似的子问题，直到最后子问题可以简单的直接求解，原问题的解即子问题的解的合并。
toc: true
authors:
  - Haoran Zhou
tags:
categories:
series:
date: '2023-12-18T13:11:22+08:00'
lastmod: '2023-12-18T13:11:22+08:00'
featuredImage:
draft: false
--- -->

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

