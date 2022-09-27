# LCP 64. 二叉树灯饰
[LCP 64. 二叉树灯饰](https://leetcode.cn/problems/U7WvvU/)
「力扣嘉年华」的中心广场放置了一个巨型的二叉树形状的装饰树。每个节点上均有一盏灯和三个开关。节点值为 0 表示灯处于「关闭」状态，节点值为 1 表示灯处于「开启」状态。每个节点上的三个开关各自功能如下：

开关 1：切换当前节点的灯的状态；
开关 2：切换 以当前节点为根 的子树中，所有节点上的灯的状态，；
开关 3：切换 当前节点及其左右子节点（若存在的话） 上的灯的状态；
给定该装饰的初始状态 root，请返回最少需要操作多少次开关，可以关闭所有节点的灯。

**示例 1：**
> 输入：root = [1,1,0,null,null,null,1]
> 输出：2
> 解释：以下是最佳的方案之一，如图所示
> ![](https://pic.leetcode-cn.com/1629357030-GSbzpY-b71b95bf405e3b223e00b2820a062ba4.gif)

**示例 2：**
> 输入：root = [1,1,1,1,null,null,1]
> 输出：1
> 解释：以下是最佳的方案，如图所示
> ![](https://pic.leetcode-cn.com/1629356950-HZsKZC-a4091b6448a0089b4d9e8f0390ff9ac6.gif)

## 思路
树的每个节点都有里两个状态（开启状态，关闭状态)。如果是基于状态的转换来思考的话可以使用树形DP。我们再来考虑开关123的状态，开关1的操作只会影响到这个节点本身，不会影响到它的子节点，开关2的操作次数会影响到它的子树，开关3的状态会影响到它的子树。并且开关2操作两次之后不会对之后的子树产生影响（因为开一次关一次）同理开关3也是如此。我们就需要两个变量来记录当前节点的祖先节点中操作了几次开关2 和 操作了几次开关3.

此时我们可以用两个bool的变量来表示开关2的次数和开关3的次数，每次只需要对其执行一次非操作便是一次开关操作。两次非操作就算作没有操作。

接下来我们就只需要深度优先遍历这棵树，在每一个节点上判断当前节点的当前状态，如果当前节点的当前状态是开灯（1） 就需要执行一次关灯操作，关灯操作有-操作一次开关1、操作一此开关2、操作一次开关3、操作一次开关123（也就是三个开关各操作一次）。再来取这四种不同的操作的最小值。

那么我们怎么判断当前节点是处于开灯状态呢？ <code>(node.val==1) == (switch2 == switch3) </code>
> 解释：如果node的值为1那么第一个括号内的表达式为<code>true</code>,此时switch2和switch3只要同号（同为true或者false）就行。
> 如果node的值为0 那么 第一个括号内的表达式为false，此时switch2和switch3只要异号就会导致当前开关打开。

那么同理，如果当前节点为关闭的状态的话，那我们依然有四种操作的方式。当前不操作、操作开关13、操作开关12、操作开关23.然后选择返回的操作数最小的进行返回。

到此为止整个流程就结束了，但是直接提交的话会超时，因为我们重复访问了部分节点，因此我们可以使用一个map来保存当前函数的结果，如果下次遇到一样参数的函数就直接返回结果便是。


### 总结
这种一个节点有不同状态时的这种情况可以考虑使用树形DP的这种方式，递归的遍历每一种状态。对每一种状态进行处理

### 代码
```golang
    /**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func closeLampInTree(root *TreeNode) int {
    // 任意一个节点会受到那些影响
    // 1. 其祖先节点的开关 2 的切换次数，偶数=不切换，奇数=切换 => 只需要用一个bool 值就可以表示
    // 2. 其父节点的是否切换了开关3 => bool值
    // 状态：（当前节点，开关2 的切换次数的奇偶行，父节点是否切换了开关3）
    type cacheS struct{
        ndoe *TreeNode
        switch2 bool
        switch3 bool
    }
    cache := map[cacheS]int{}
    // 返回从当前状态出发，最少需要操作多少次
    // switch2表示是否动了开关2 switch3表示是否动了开关3
    var dfs func(node *TreeNode,switch2,switch3 bool)int
    dfs = func(node *TreeNode,switch2,switch3 bool)int{
        if node == nil{
            return 0
        }
        p := cacheS{node,switch2,switch3}
        if res,ok := cache[p];ok{
            return res
        }
        res := 0
        // 当前节点的灯是开还是关？
        // 灯 = 开，且switch2 和 switch3 低消，最终还是开
        // 灯 = 关，且switch2 和 switch2 无法抵消，最终是开的
        if (node.Val == 1) == (switch2 == switch3){
            // 当前灯的状态是开启状态，最终要到关闭状态，因此有动开关1、动开关2、动开关3、动开关123的方式
            // 有三个开关，枚举打开1 一个开关，后者3个开关

            // 这个是打开开关1，此时不会影响它的子节点，因此第二个参数传switch2 此时开关3 没有动 因此是false 
            // 最后+1是因为开了开关1
            res1 := dfs(node.Left,switch2,false) + dfs(node.Right,switch2,false) + 1
            // 切换开关2
            res2 := dfs(node.Left,!switch2,false) + dfs(node.Right,!switch2,false) + 1
            // 切换开关3
            res3 := dfs(node.Left,switch2,true) + dfs(node.Right,switch2,true) + 1
            // 切换开关123
            res123 := dfs(node.Left,!switch2,true) + dfs(node.Right,!switch2,true) + 3 //+3是因为每个开关各操作了一次，因此总共操作了三次
            res =  min([]int{res1,res2,res3,res123})
        }else{
            //  这种情况就是灯的开关是处于关闭的状态，最后要的也是关闭状态
            // 在这种情况下动开关的情况是 一个都不打开，打开两个开关
            res0 := dfs(node.Left,switch2,false) + dfs(node.Right,switch2,false)
            res12 := dfs(node.Left,!switch2,false) + dfs(node.Right,!switch2,false) + 2
            res13 := dfs(node.Left,switch2,true) + dfs(node.Right,switch2,true) + 2
            res23 := dfs(node.Left,!switch2,true) + dfs(node.Right,!switch2,true) + 2
            res =  min([]int{res0,res12,res13,res23})
        }
        cache[p] = res
        return res
    }

    return dfs(root,false,false)
}

func min(nums []int)int{
    res := nums[0]
    for _,v := range nums{
        if v < res{
            res = v
        } 
    }
    return res
}
```






