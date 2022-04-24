---
title: 'LeetCode Notebook'
date: 2021-10-14 17:30:19
tags: [Study]
---
Today is a nice day!
### 二分查找
二分查找易错点：**写二分法经常写乱，主要是因为对区间的定义没有想清楚，区间的定义就是不变量**。
在\[A, B]的情况下代码应该这样写：
``` C++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
        while (left <= right) { // 当left==right，区间[left, right]依然有效，所以用 <=
            int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
            if (nums[middle] > target) {
                right = middle - 1; // target 在左区间，所以[left, middle - 1]
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，所以[middle + 1, right]
            } else { // nums[middle] == target
                return middle; // 数组中找到目标值，直接返回下标
            }
        }
        // 未找到目标值
        return -1;
    }
};
```

在\[A, B)的情况下，代码应该这样写：
``` C++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size(); // 定义target在左闭右开的区间里，即：[left, right)
        while (left < right) { // 因为left == right的时候，在[left, right)是无效的空间，所以使用 <
            int middle = left + ((right - left) >> 1);
            if (nums[middle] > target) {
                right = middle; // target 在左区间，在[left, middle)中
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，在[middle + 1, right)中
            } else { // nums[middle] == target
                return middle; // 数组中找到目标值，直接返回下标
            }
        }
        // 未找到目标值
        return -1;
    }
};
```
**其中重点在于右边界值到底是否存在：**
如果右边界值存在，也就是区间为\[A, B]，那么while里面可以写成left <= right，在 left == right的情况下还能判断一次，nums\[mid]的值是否存在。在left和right取值时需要将mid排除出去。
如果右边界值不存在，也就是区间为\[A, B), 那么while里面只能写成left < right，因为right取不到所以并不成立。相应的nums\[mid]只有在left移动时才能使用mid+1将mid排除，因为区间为**前闭后开**right在移动至mid时已经将mid排除了。

### 双指针和快慢指针
其实差距不大，其实就是两个指针比较然后移动。


