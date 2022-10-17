# [剑指 Offer 22. 链表中倒数第k个节点](https://leetcode.cn/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

 [剑指 Offer 22. 链表中倒数第k个节点](https://leetcode.cn/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。

例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。



**示例：**

```
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.
```



## 思路

双指针，不管是返回倒数的多少个节点还是说求中间节点这类在链表当中求距离的，基本上都是双指针，一个快指针一个慢指针。事先预定好他们的间隔距离，然后再一起走到快指针结束（或者满足条件）即可。



在这道题中就先让快指针走k步，然后再快慢指针一起走，当快指针走到链表结尾的时候，慢指针自然就是倒数第k个了。



## 代码

### golang

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func getKthFromEnd(head *ListNode, k int) *ListNode {
    fast,low := head,head
    for i:=1;i<=k;i++{
        fast = fast.Next
    }
    for fast!=nil{
        fast = fast.Next
        low = low.Next
    }
    return low
}
```

