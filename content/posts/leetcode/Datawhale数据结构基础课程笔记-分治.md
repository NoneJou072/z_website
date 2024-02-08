---
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
---

{{< katex >}}

> **分治算法（Divide and Conquer）**：字面上的解释是「分而治之」，就是把一个复杂的问题分成两个或更多的相同或相似的子问题，直到最后子问题可以简单的直接求解，原问题的解即子问题的解的合并。

分治算法从实现方式上可以分为两种：「递归算法」和「迭代算法」。

## 1.3 分治算法的适用条件
分治算法能够解决的问题，一般需要满足以下 
 个条件：

可分解：原问题可以分解为若干个规模较小的相同子问题。
子问题可独立求解：分解出来的子问题可以独立求解，即子问题之间不包含公共的子子问题。
具有分解的终止条件：当问题的规模足够小时，能够用较简单的方法解决。
可合并：子问题的解可以合并为原问题的解，并且合并操作的复杂度不能太高，否则就无法起到减少算法总体复杂度的效果了。

## 2. 分治算法的基本步骤

1. 分解：把要解决的问题分解为成若干个规模较小、相对独立、与原问题形式相同的子问题。
2. 求解：递归求解各个子问题。
3. 合并：按照原问题的要求，将子问题的解逐层合并构成原问题的解。
其中第 1 步中将问题分解为若干个子问题时，最好使子问题的规模大致相同。换句话说，将一个问题分成大小相等的 k 个子问题的处理方法是行之有效的。在许多问题中，可以取 k=2。这种使子问题规模大致相等的做法是出自一种平衡子问题的思想，它几乎总是比子问题规模不等的做法要好。

其中第 2 步的「递归求解各个子问题」指的是按照同样的分治策略进行求解，即通过将这些子问题分解为更小的子子问题来进行求解。就这样一直分解下去，直到分解出来的子问题简单到只用常数操作时间即可解决为止。

在完成第 2 步之后，最小子问题的解可用常数时间求得。然后我们再按照递归算法中回归过程的顺序，由底至上地将子问题的解合并起来，逐级上推就构成了原问题的解。

按照分而治之的策略，在编写分治算法的代码时，也是按照上面的 3 个步骤来编写的，其对应的伪代码如下：
```python
def divide_and_conquer(problems_n):             # problems_n 为问题规模
    if problems_n < d:                          # 当问题规模足够小时，直接解决该问题
        return solove()                         # 直接求解
    
    problems_k = divide(problems_n)             # 将问题分解为 k 个相同形式的子问题
    
    res = [0 for _ in range(k)]                 # res 用来保存 k 个子问题的解
    for problem_k in problems_k:
        res[i] = divide_and_conquer(problem_k)  # 递归的求解 k 个子问题
    
    ans = merge(res)                            # 合并 k 个子问题的解
    return ans                                  # 返回原问题的解
```

参考：[04.02.05 分治算法](https://github.com/datawhalechina/leetcode-notes/blob/main/docs/ch04/04.02/04.02.05-Divide-And-Conquer-Algorithm.md)

## [912. 排序数组](https://leetcode.cn/problems/sort-an-array/description/)

思路：
1. 我们使用**归并排序**来解决这个排序问题，用到了分治的思想。
    ```cpp
    class Solution {
    private:
        vector<int> tmp;
        void mergeSort(vector<int>& nums, int l, int r){
            if(l >= r) return;
            int mid = (l + r) >> 1;

            mergeSort(nums, l, mid);
            mergeSort(nums, mid + 1, r);

            int i = l, j = mid + 1;
            int cnt = 0;
            while(i <= mid && j <= r){
                if(nums[i] <= nums[j]){
                    tmp[cnt++] = nums[i++];
                }
                else{
                    tmp[cnt++] = nums[j++];
                }
            }

            while(i <= mid){
                tmp[cnt++] = nums[i++];
            }
            while(j <= r){
                tmp[cnt++] = nums[j++];
            }

            for(int i = 0; i < r - l + 1; ++i){
                nums[i + l] = tmp[i];
            }
    
        }

    public:
        vector<int> sortArray(vector<int>& nums) {
            tmp.resize(nums.size(), 0);
            mergeSort(nums, 0, nums.size() - 1);
            return nums;
        }
    };

    ```