---
title: Datawhale数据结构基础课程笔记-(Day1-2)枚举
description: 枚举算法（Enumeration Algorithm）也称为穷举算法，指的是按照问题本身的性质，一一列举出该问题所有可能的解，并在逐一列举的过程中，将它们逐一与目标状态进行比较以得出满足问题要求的解。在列举的过程中，既不能遗漏也不能重复。
toc: true
authors:
  - Haoran Zhou
tags:
categories:
series:
date: '2023-12-12T13:11:22+08:00'
lastmod: '2023-12-12T13:11:22+08:00'
featuredImage:
draft: false
---
{{< katex >}}
> **枚举算法（Enumeration Algorithm）**：也称为穷举算法，指的是按照问题本身的性质，一一列举出该问题所有可能的解，并在逐一列举的过程中，将它们逐一与目标状态进行比较以得出满足问题要求的解。在列举的过程中，既不能遗漏也不能重复。

## [1. 两数之和](https://leetcode.cn/problems/two-sum/description/)

思路：
1. 根据枚举的思想，我们需要遍历数组，并建立目标函数：
    $$
    array[x] + array[y] = target
    $$
    我们从头遍历 x 和 y，直到满足上面等式后返回 x，y：

    ```cpp
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            vector<int> ret;
            for(int x = 0; x < nums.size(); x++){
                for(int y = 0; y < nums.size(); y++){
                    if(x != y && nums[x] + nums[y] == target){
                        ret.push_back(x);
                        ret.push_back(y);
                        break;
                    }
                }
                if(ret.size() == 2){
                    break;
                }
            }
            return ret;
        }
    };

    ```

    执行用时分布 844ms  
    消耗内存分布 10.33MB  


## [204. 计数质数](https://leetcode.cn/problems/count-primes/)

思路：
1. 质数是只能被自己整除的自然数（0，1除外）。
    根据枚举的思想，我们从2开始遍历x，每次再从2开始遍历一次y，看y是否能被x整除，以判断是否为质数，是则计数加一。
    ```cpp
    class Solution {
    public:
        int countPrimes(int n) {
            int count = 0;
            bool is_prime = true;
            for(int x = 2; x < n; x++){
                for(int y = 2; y < x; y++){
                    if(x % y == 0){
                        is_prime = false;
                        break;
                    }
                    is_prime = true;
                }
                if(is_prime){
                count++;
                }
            }
            return count;
        }
    };

    ```
    但这样在`n = 499979`时会超出时间限制。因此枚举方法在此题不推荐使用。


## [1925. 统计平方和三元组的数目](https://leetcode.cn/problems/count-square-sum-triples/)

思路：
1. 根据枚举的思想，我们设置三重循环：从1开始遍历a，再嵌套从1开始遍历b，再再嵌套从1开始遍历c，判断是否满足等式。

    ```cpp
    class Solution {
    public:
        int countTriples(int n) {
            int count = 0;
            for(int a = 1; a < n + 1; a++){
                for(int b = 1; b < n + 1; b++){
                    for(int c = 1; c < n + 1; c++){
                        if(a*a + b*b == c*c){
                            count++;
                        }
                    }
                }
            }
            return count;
        }
    };
    ```

    执行用时分布352ms
    消耗内存分布6.15MB

    时间复杂度：$O(n^3)$。
    空间复杂度：$O(1)$。

2. 对上面的思路进一步优化。我们可以在每次改变a，b的值后直接根据公式计算c的值，通过判断c的值是否小于等于n来判断等式是否成立，从而减少一次循环嵌套。

    注意：在计算中，为了防止浮点数造成的误差，并且两个相邻的完全平方正数之间的距离一定大于 $1$，所以我们可以用 $\sqrt{a^2 + b^2 + 1}$ 来代替 $\sqrt{a^2 + b^2}$。

    ```cpp
    class Solution {
    public:
        int countTriples(int n) {
            int count = 0;
            for(int a = 1; a < n + 1; a++){
                for(int b = 1; b < n + 1; b++){
                    int c = sqrt(a*a + b*b + 1);
                    if(c <= n && a*a + b*b == c*c){
                        count++;
                    }
                }
            }
            return count;
        }
    };
    ```
    执行用时分布8ms
    消耗内存分布6.17MB

    时间复杂度：$O(n^2)$。
    空间复杂度：$O(1)$。

## [2427. 公因子的数目](https://leetcode.cn/problems/number-of-common-factors/description/)
思路：
1. 如果 x 可以同时整除 a 和 b ，则认为 x 是 a 和 b 的一个 公因子 。
可以从1开始，遍历到a、b中较小的一个值，每次判断是否满足整除等式。
    ```cpp
    class Solution {
    public:
        int commonFactors(int a, int b) {
            int count = 0;
            for(int i = 1; i < min(a, b) + 1; i++){
                if(a % i == 0 && b % i == 0){
                    count++;
                }
            }
            return count;
        }
    };
    ```

    执行用时分布4ms
    消耗内存分布6.38MB
    
## [LCR 180. 文件组合](https://leetcode.cn/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/description/)
思路：
1. 根据题意, 大于等于 target 的数显然不需要考虑，我们从零开始遍历到目标数字`target`. 每次遍历时维护一个列表并将当前遍历的值`x`作为首元素。  
    每次遍历中，我们查找以`x`开头并使列表元素和为`target`的其他元素，从`x`开始遍历到`target`，将每次遍历的值`y`加入到列表中，如果列表元素和超过了`target`，则退出遍历。

    ```cpp
    class Solution {
    public:
        vector<vector<int>> fileCombination(int target) {
            vector<vector<int>> ret;
            for(int x = 1; x < target; x++){
                vector<int> temp;
                int sum = 0;
                for(int y = x; y < target; y++){
                    temp.push_back(y);
                    sum += y;
                    if(temp.size() > 1 && sum == target){
                        ret.push_back(temp);
                        break;
                    }
                }
            }
            return ret;
        }
    };
    ```
    这种方法，当target =50252时会超出时间限制。
2. 由于组合长度需要大于2， 所以遍历条件可以改成`target/2`，但还是会超出时间限制。我们在第二层循环中令 `sum > target` 时直接 `break`，测试通过。
3. 
    ```
    输入
    target =
    93
    输出
    [[13,14,15,16,17,18],[30,31,32]]
    预期结果
    [[13,14,15,16,17,18],[30,31,32],[46,47]]

    ```
    原因是在C++中，整数除法的结果会舍弃小数部分，保留整数部分。 `target` 是 `int` 类型的，此时 `target/2` 等于 63（向下取整），而不是 63.5. 我们将 `x` 的判定条件改成`x <= target/2` 即可。
    ```cpp
    class Solution {
    public:
        vector<vector<int>> fileCombination(int target) {
            vector<vector<int>> ret;
            for(int x = 1; x <= target/2; x++){
                vector<int> temp;
                int sum = 0;
                for(int y = x;; y++){
                    temp.push_back(y);
                    sum += y;
                    if(temp.size() > 1 && sum == target){
                        ret.push_back(temp);
                        break;
                    }
                    else if(sum > target){
                        break;
                    }
                }
            }
            return ret;
        }
    };
    ```
    执行用时分布328ms
    消耗内存分布96.30MB
    
## [2249. 统计圆内格点数目](https://leetcode.cn/problems/count-lattice-points-inside-a-circle/description/)
给你一个二维整数数组 `circles` ，其中 `circles[i] = [xi, yi, ri]` 表示网格上圆心为 (xi, yi) 且半径为 ri 的第 i 个圆，返回出现在 至少一个 圆内的 格点数目 。

思路：
1. 判定一个点是否在圆内，只需要判定这个点到圆心的距离是否小于等于半径。
    由于 `1 <= xi, yi <= 100`，可以使用暴力枚举法来解决这个问题，即遍历网格中的每个点，对上述不等式作判定。
    ```cpp
    class Solution {
    public:
        int countLatticePoints(vector<vector<int>>& circles) {
            int count = 0;
            for(int x = 0; x < 100; x++){
                for(int y = 0; y < 100; y++){
                    if(point_in_circle(x, y, circles)){
                        count++;
                    }
                }
            }
            return count;
        }
        bool point_in_circle(int x, int y, vector<vector<int>>& circles){
            for(int i = 0; i < circles.size(); i++){
                if((x-circles[i][0])*(x-circles[i][0]) + (y-circles[i][1])*(y-circles[i][1]) <= circles[i][2]*circles[i][2]){
                    return true;
                }
            }
            return false;
        }
    };

    ```
2. 第 51 个测试用例发生报错。原因是需要x，y表示圆心的最大坐标，我们在网格中遍历时的最大坐标值需要乘以2.
    ```cpp
    class Solution {
        public:
            int countLatticePoints(vector<vector<int>>& circles) {
                int count = 0;
                for(int x = 0; x <= 200; x++){
                    for(int y = 0; y <= 200; y++){
                        if(point_in_circle(x, y, circles)){
                            count++;
                        }
                    }
                }
                return count;
            }
            bool point_in_circle(int x, int y, vector<vector<int>>& circles){
                for(int i = 0; i < circles.size(); i++){
                    if((x-circles[i][0])*(x-circles[i][0]) + (y-circles[i][1])*(y-circles[i][1]) <= circles[i][2]*circles[i][2]){
                        return true;
                    }
                }
                return false;
            }
        };

    ``` 
    执行用时分布1008ms
    消耗内存分布8.13MB

---
以上，第1-2天的学习题目已经作答完毕。不过，每个题目都有更优的解法，我们还需要不断地学习。
