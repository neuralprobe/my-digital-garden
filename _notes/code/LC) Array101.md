---
title: LC) Array101
date: 2022-09-06
tags: leetCode array python cpp dataStructure 
---

# LeetCode Array101

---

## Basic concepts:

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



---
**Reference**
- [Array101](https://leetcode.com/explore/learn/card/fun-with-arrays/)

**Related Internal-Links**
