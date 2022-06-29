# 摆动排序 II
<a href="https://leetcode.cn/problems/wiggle-sort-ii/">leetcode链接</a><br>
给你一个整数数组 nums，将它重新排列成 nums[0] < nums[1] > nums[2] < nums[3]... 的顺序。

你可以假设所有输入数组都可以得到满足题目要求的结果

示例 1：

```
输入：nums = [1,5,1,1,6,4]
输出：[1,6,1,5,1,4]
解释：[1,4,1,5,1,6] 同样是符合题目要求的结果，可以被判题程序接受。
```
示例 2：

```
输入：nums = [1,3,2,2,3,1]
输出：[2,3,1,3,1,2]
```

提示：

1 <= nums.length <= 5 * 104

0 <= nums[i] <= 5000

题目数据保证，对于给定的输入 nums ，总能产生满足题目要求的结果

进阶：你能用 O(n) 时间复杂度和 / 或原地 O(1) 额外空间来实现吗？

## 思路
当第一眼看到这道题的时候，我想到的是直接将该数组进行排序，排序之后从中间截断，中间点使用<code>mid = (nums.length+1)/2</code>向上取整。从中间分为两份之后前面一份隔一个空填一个数字，后面部分插空。写完代码开开心心提交，提交之后发现问题 4，5，5，6这样的数字就会出问题，因为两个5挨着了而且是在中间所以才会出问题。那么为了避免这个问题，可以将中间相同的数字尽量的分散到两边，因此可以将排序后的数字进行反转，在反转后间隔插入便可解

## 代码
### java
``` java
class Solution {
    public void wiggleSort(int[] nums) {
          Arrays.sort(nums);
          int temp[] = nums.clone();
          int len = nums.length;
          int mid = (len+1)/2;
          int k = mid-1;
          int j = len-1;

          for(int i=0;i<len;i+=2){
              nums[i] = temp[k--];
              if(i+1<len){
                 nums[i+1] = temp[j--];
              }
    
          }
    }
}
```