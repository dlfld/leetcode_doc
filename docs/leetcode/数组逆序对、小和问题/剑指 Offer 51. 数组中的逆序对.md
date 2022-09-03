# 剑指 Offer 51. 数组中的逆序对
[剑指 Offer 51. 数组中的逆序对](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)
在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

**示例 1:**
```
输入: [7,5,6,4]
输出: 5
```
**限制：**
```
0 <= 数组长度 <= 50000
```

## 思路
第一种思路是暴力遍历两次O(n^2)，没试过能不能过。
第二种思路使用归并排序的思想，在归操作的时候对逆序的数对进行计数
> 如下图所示，为数组[7,3,2,6,0,1,5,4] 的归并排序过程。

![](https://pic.leetcode-cn.com/1614274007-nBQbZZ-Picture1.png)
计数的过程发生在合并的时候，合并的时候只需要考虑左边和右边的大小比较，因为同一边的在合并的时候已经计数过了。

**第一轮**
+ 合并73 73为逆序，右边数组的3 小于7，因为左边和右边分别是从小到大排序的，因此3小于7的话，3就小于7后面的每一个数，在这一对中7后面只有7，因此计数count+1
+ 合并26 26为顺序，不计数
+ 合并01 01为顺序，不计数
+ 合并54 54为逆序，同理 count+1
**第二轮** 
+ 合并37 26
  + 当left指针指向3 right指针指向2时，rightValue < leftvalue,此时rightValue小于left指针以及以后的所有元素，因此count+2 （3，7两个元素）
  + 同理当right指向6，left指向3时，count+ 1
+ 以此类推，可得到最终结果


## 代码
### Go
```golang
    func reversePairs(nums []int) int {
     result := 0
     var mergeSort func(nums[]int,left,right int) []int
     var merge func(leftSort,rightSord []int)[]int
     mergeSort = func (nums[]int,left,right int) []int{
        if left == right{
            return []int{nums[left]}
        }
        mid := left + ((right-left)>>1)
        leftSort := mergeSort(nums,left,mid)
        rightSord := mergeSort(nums,mid+1,right)
        return merge(leftSort,rightSord)
    }
    merge = func (leftSort,rightSord []int)[]int{
        res:=[]int{}
        lp,rp := 0,0
        for lp<len(leftSort) && rp < len(rightSord){
            if leftSort[lp] <= rightSord[rp]{
                res = append(res,leftSort[lp])
                lp++
            }else{
                result+=len(leftSort) - lp
                res = append(res,rightSord[rp])
                rp++
            }
        }
        if lp < len(leftSort){
            res = append(res,leftSort[lp:]...)
        }
        if rp < len(rightSord){
            res = append(res,rightSord[rp:]...)
        }
        return res
    }
    if len(nums) == 0{
        return 0
    }
    mergeSort(nums,0,len(nums)-1)
    return result
}
```