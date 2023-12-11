> **枚举算法（Enumeration Algorithm）**：也称为穷举算法，指的是按照问题本身的性质，一一列举出该问题所有可能的解，并在逐一列举的过程中，将它们逐一与目标状态进行比较以得出满足问题要求的解。在列举的过程中，既不能遗漏也不能重复。

## 1. [两数之和](https://leetcode.cn/problems/two-sum/description/)

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


## 204. [计数质数](https://leetcode.cn/problems/count-primes/)

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

## 1925. [统计平方和三元组的数目](https://leetcode.cn/problems/count-square-sum-triples/)

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
