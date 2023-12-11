> **枚举算法（Enumeration Algorithm）**：也称为穷举算法，指的是按照问题本身的性质，一一列举出该问题所有可能的解，并在逐一列举的过程中，将它们逐一与目标状态进行比较以得出满足问题要求的解。在列举的过程中，既不能遗漏也不能重复。

## 1. 两数之和
[题目描述](https://leetcode.cn/problems/two-sum/description/)

思路历程：
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
    显然我们需要对这段程序进行优化


