---
title: Datawhale数据结构基础课程笔记-(Day7-9)回溯
description: 回溯算法（Backtracking）：一种能避免不必要搜索的穷举式的搜索算法。采用试错的思想，在搜索尝试过程中寻找问题的解，当探索到某一步时，发现原先的选择并不满足求解条件，或者还需要满足更多求解条件时，就退回一步（回溯）重新选择，这种走不通就退回再走的技术称为「回溯法」，而满足回溯条件的某个状态的点称为「回溯点」。
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
---

{{< katex >}}

> **回溯算法（Backtracking）**：一种能避免不必要搜索的穷举式的搜索算法。采用试错的思想，在搜索尝试过程中寻找问题的解，当探索到某一步时，发现原先的选择并不满足求解条件，或者还需要满足更多求解条件时，就退回一步（回溯）重新选择，这种走不通就退回再走的技术称为「回溯法」，而满足回溯条件的某个状态的点称为「回溯点」。

**回溯算法的基本思想**：以深度优先搜索的方式，根据产生子节点的条件约束，搜索问题的解。当发现当前节点已不满足求解条件时，就「回溯」返回，尝试其他的路径。

具体步骤如下：
1. 明确所有选择：画出搜索过程的决策树，根据决策树来确定搜索路径。
2. 明确终止条件：推敲出递归的终止条件，以及递归终止时的要执行的处理方法。
3. 将决策树和终止条件翻译成代码：
    * 定义回溯函数（明确函数意义、传入参数、返回结果等）。
    * 书写回溯函数主体（给出约束条件、选择元素、递归搜索、撤销选择部分）。
    * 明确递归终止条件（给出递归终止条件，以及递归终止时的处理方法）。

回溯算法的通用模板：
```python
res = []    # 存放所欲符合条件结果的集合
path = []   # 存放当前符合条件的结果
def backtracking(nums):             # nums 为选择元素列表
    if 遇到边界条件:                  # 说明找到了一组符合条件的结果
        res.append(path[:])         # 将当前符合条件的结果放入集合中
        return

    for i in range(len(nums)):      # 枚举可选元素列表
        path.append(nums[i])        # 选择元素
        backtracking(nums)          # 递归搜索
        path.pop()                  # 撤销选择

backtracking(nums)
```


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

