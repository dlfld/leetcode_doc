# [剑指 Offer II 021. 删除链表的倒数第 n 个结点](https://leetcode.cn/problems/SLwz0R/)

 [剑指 Offer II 021. 删除链表的倒数第 n 个结点](https://leetcode.cn/problems/SLwz0R/)



给定一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**示例 1：**

![](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)



```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**示例 2：**

```
输入：head = [1], n = 1
输出：[]
```

**示例 3：**

```
输入：head = [1,2], n = 1
输出：[1]
```

**提示：**

+ 链表中结点的数目为 `sz`
+ `1 <= sz <= 30`
+ `0 <= Node.val <= 100`
+ `1 <= n <= sz`







## 思路

**方法一**

最简单的方法就是遍历一遍链表，计算出链表的长度，然后再一次遍历链表，删除倒数第k个节点即可



**方法二**

双指针：使用一个指针在前一个指针在后，前面的指针超前后面的指针n个节点（他们相隔n-1个节点），当前面指针走到头的时候后面的指针就可以删除当前节点的下一个节点了。在双指针使用的时候自己初始化一个头节点，免得[1]. 1这种情况过不了





## 代码

### Golang

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    res := &ListNode{0,head}
    p1,p2 := res,res
    for i:=0;i<=n;i++{
        p2 = p2.Next
    }
    for p2 != nil{
        p2 = p2.Next
        p1 = p1.Next
    }
    p1.Next = p1.Next.Next
    return res.Next
}
```



