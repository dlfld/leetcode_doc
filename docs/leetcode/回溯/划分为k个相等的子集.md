# 698. 划分为k个相等的子集
[698. 划分为k个相等的子集](https://leetcode.cn/problems/partition-to-k-equal-sum-subsets/)


给定一个整数数组  nums 和一个正整数 k，找出是否有可能把这个数组分成 k 个非空子集，其总和都相等。

**示例 1：**
```
输入： nums = [4, 3, 2, 3, 5, 2, 1], k = 4
输出： True
说明： 有可能将其分成 4 个子集（5），（1,4），（2,3），（2,3）等于总和。

```

**示例 2:**

```
输入: nums = [1,2,3,4], k = 3
输出: false
```
提示：
+ 1 <= k <= len(nums) <= 16
+ 0 < nums[i] < 10000
+ 每个元素的频率在 [1,4] 范围内


## 思路

首先我们需要将n个数字均分成k组，并且每一组的和都要一样，所以我们可以先排除掉不能够均分的情况再做后面的操作。

**排除不满足均分情况**

首先对整个数组求和，求和之后看看能不能均分成k等分，如果不可以的话直接返回false
```golang
    sum := 0
    sort.Ints(nums)
    for _,v := range nums{
        sum += v
    }
    if sum % k != 0{
        return false
    }

```

**排除特殊不满足情况之后就可以进行下面的操作了**

首先我们能够想到的暴力算法就是穷举每一种可能，然后去判断每一种可能是不是有一种可能满足题意。满足穷举的算法首先就可以想到回溯。
在回溯时我们可以先实现最为暴力的回溯（不剪枝），后面再添加剪枝算法。

在这道题中，我们可以将nums数组中的数字想成是n个权重为nums[i]的球，我们现在需要将这些球装到k个桶里面去，需要满足的条件是最后装完球之后每个桶的总权重都一样。

我们可以用index代表这是第index个球，我们将第index个球依次放入第1-k个桶，如果第index个球刚好能够放入第i个桶内时，便一直递归的放第index+1个球，一直到最后一个球放不下去的时候便一层一层的往上回溯。如果放到最后每一个球都放入了桶内，此时这个便是最终的结果。

判断结束的情况

``` golang
    // 如果走到了最后 则return true 结束
    if index == len(nums){
        return true
    }
```
在暴力的情况下肯定是会超时的，因此我们可以采取一定的剪枝算法；

1. 如果当前桶和上一个桶内装的球总权值一样的话，下一次的回溯就直接跳过了（因为已经算过了）
```golang
backtracking =  func (bucket []int,index int)bool{
        // 如果走到了最后 则return true 结束
        if index == len(nums){
            return true
        }
        for i:=0;i<k;i++{
            // 如果当前桶的值和上一个桶的值是一样的，则表示不需要再回溯这个桶了（没必要O）
            if i > 0 && bucket[i] == bucket[i-1]{
                continue
            }
            // 排除掉加上超过了总值的
            if bucket[i] + nums[index] > target{
                continue
            }
            bucket[i] += nums[index]
            temp := backtracking(bucket,index+1)
            if temp{
                return true
            }
            bucket[i] -= nums[index]
        }
        return false
    }
```
## 总结
    这一类挑选子集的题可以先想到回溯试一下。将球放进桶中的回溯看一下当前回溯的返回方式。
##  代码
### Golang
```golang

func canPartitionKSubsets(nums []int, k int) bool {
    sum := 0
    sort.Ints(nums)
    for _,v := range nums{
        sum += v
    }
    if sum % k != 0 {
        return false
    }
    sort.Sort(sort.Reverse(sort.IntSlice(nums)))
    target := sum / k

    var backtracking  func (bucket []int,index int)bool
    backtracking =  func (bucket []int,index int)bool{
        // 如果走到了最后 则return true 结束
        if index == len(nums){
            return true
        }
        for i:=0;i<k;i++{
            // 如果当前桶的值和上一个桶的值是一样的，则表示不需要再回溯这个桶了（没必要O）
            if i > 0 && bucket[i] == bucket[i-1]{
                continue
            }
            // 排除掉加上超过了总值的
            if bucket[i] + nums[index] > target{
                continue
            }
            bucket[i] += nums[index]
            temp := backtracking(bucket,index+1)
            if temp{
                return true
            }
            bucket[i] -= nums[index]
        }
        return false
    }
    return backtracking(make([]int,k),0)
    // return res
}


```

