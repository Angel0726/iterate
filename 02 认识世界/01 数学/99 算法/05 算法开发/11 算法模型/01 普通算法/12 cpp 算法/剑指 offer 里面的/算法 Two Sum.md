---
title: 算法 Two Sum
toc: true
date: 2018-06-25 16:09:43
---
# 题目地址：

https://leetcode.com/problems/two-sum/description/

# Description:


Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **_exactly_** one solution, and you may not use the _same_ element twice.


# **Example:**




    Given nums = [2, 7, 11, 15], target = 9,

    Because nums[<b>0</b>] + nums[<b>1</b>] = 2 + 7 = 9,
    return [<b>0</b>, <b>1</b>].




# 我的想法：


遍历然后找到和满足要求的，注意 list 里面的重复的数据和 list 边界


# 我的解法：




    class Solution:
        def twoSum(self, nums, target):
            """
            :type nums: List[int]
            :type target: int
            :rtype: List[int]
            """
            l=len(nums)
            for i in range(l):
                if len(nums)<=0:
                    return []
                a=nums.pop(0)
                b=target-a
                if b in nums:
                    return [i,nums.index(b)+i+1]


用了 796ms，找到了一个用 62ms 的解法：


# 别人的解法：




    class Solution:
        def twoSum(self, nums, target):
            """
            :type nums: List[int]
            :type target: int
            :rtype: List[int]
            """

            d = {}
            for ind1 in range(len(nums)):
                n2 = target - nums[ind1]
                if n2 in d:
                    return d[n2], ind1
                else:
                    d[nums[ind1]] = ind1





#  别人的解法的思路：


将已经查找过的数据存放在字典里，使用数字本身作为 key，index作为 value，这样查找的时候可以直接根据值查找 key 看是不是存在这个值，而且如果有的话，轻易地把根据 key 获取对应的 value 即 index。




# COMMENT：

