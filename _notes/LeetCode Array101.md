---
title: LeetCode Array101
date: 2022-09-06
tags: LeetCode Array Python C++ DataStructure 
---

# LeetCode Array101

---

# Introduction

> An Array is a ==collection of items==. The items could be integers, strings, DVDs, games, books—anything really. The items are stored in ==neighboring (contiguous) memory locations==. Because they're stored together, checking through the entire collection of items is straightforward.

- Accessing elements in arrays

- Array capacity vs length

---

## Max consecutive ones

> Given a binary array `nums`, return _the maximum number of consecutive_ `1`_'s in the array_.

```python
class Solution:
    def findMaxConsecutiveOnes(self, nums: List[int]) -> int:
        maxCount = 0
        prevMaxCount = 0
        for num in nums:
            if num:
                maxCount += 1
            else:
                prevMaxCount = max(maxCount, prevMaxCount)
                maxCount = 0
        return max(prevMaxCount, maxCount)
```

```cpp
class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        int count = 0;
        int maxCount = 0;
        for (int num : nums){
            if (num) count++;
            else {
                maxCount = max(maxCount, count);
                count = 0;
            }
        }
        return max(maxCount, count);
    }    
};
```

---

## Find Numbers with Even Number of Digits

> Given an array `nums` of integers, return how many of them contain an **even number** of digits.

```cpp
class Solution {
public:    
    int countEven(int num){
        int count = 0;
        while(true){
            if (num >= 10){
                num = num/10;
                count ++;
            }
            else {
                count ++;
                break;            
            }
            
        }
        if (count%2 == 0) return true;
        else return false;
    }    
    
    int findNumbers(vector<int>& nums) {
        int count = 0;
        for (int num : nums){
            if (countEven(num)) count++;
        }
        return count;        
    }
};
```


---

## Squares of a Sorted Array

> Given an integer array `nums` sorted in **non-decreasing** order, return _an array of **the squares of each number** sorted in non-decreasing order_.

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        //first find the minimum abs idx
        int least_abs = Solution::findLeastAbsolute(nums);
        //Register small squared value by comparing L/R pointers
        int Lp = least_abs;
        int Rp = least_abs;
        vector<int> output;
        int len = nums.size();
        output.push_back(pow(nums[Lp],2));
        while (true){
            if (Lp >0 && Rp <(len-1)){
                if (abs(nums[Lp-1]) < abs(nums[Rp+1])){
	                output.push_back(pow(nums[--Lp],2));
                }
                else output.push_back(pow(nums[++Rp],2));
            }
            else if (Lp ==0 && Rp <(len-1)){
                output.push_back(pow(nums[++Rp],2));
            }   
            else if (Lp > 0 && Rp == (len-1)){
                output.push_back(pow(nums[--Lp],2));
            }
            else break;
        }
        return output;
    }
    
  int findLeastAbsolute(vector<int>& nums) {
        int idx = 0;
        int len = nums.size();
        while (nums[idx] < 0) {
            idx++; 
            if (idx <0 or idx >= len) break;
        }
        // if idx == 0, all posive
        if (idx==0) return idx;
        // if idx == len, all negative => L-pointer only
        if (idx == len) return (idx-1);                
        // if idx <> len, neg-pos switch in the middle
        if (idx > 0 && abs(nums[idx]) <= abs(nums[idx-1])) return idx;
        if (idx > 0 && abs(nums[idx]) > abs(nums[idx-1])) return (idx-1);
        return idx;
    }
};
```

---

# Reference

- [Array101](https://leetcode.com/explore/learn/card/fun-with-arrays/)
