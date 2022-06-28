# 买卖股票的最佳时机 II
<a href="https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/">leetcode链接</a><br>
给你一个整数数组 prices ，其中 prices[i] 表示某支股票第 i 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候 最多 只能持有 一股 股票。你也可以先购买，然后在 同一天 出售。

返回 你能获得的 最大 利润 。

示例 1：

```
输入：prices = [7,1,5,3,6,4]
输出：7
解释：在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6 - 3 = 3 。
     总利润为 4 + 3 = 7 。
```
示例 2：
```
输入：prices = [1,2,3,4,5]
输出：4
解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
     总利润为 4 。
```
示例 3：
```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 交易无法获得正利润，所以不参与交易可以获得最大利润，最大利润为 0 。
```
提示：

1 <= prices.length <= 3 * 104

0 <= prices[i] <= 104

## 思路
本题可以认为全局最优的股票买卖是从第一天的股票买卖方式一步一步推出来的。因此可以使用**动态规划**从第一天开始一天一天的往后推，直到推到最后一天。

**1. 确定dp数组下标及含义**

可以设搭配数组为一个n(天数)X 2的矩阵dp[i][j], 其中dp[i][0] 代表第i天结束之后不持有股票的最大利润，dp[i][1]代表第i天结束以后手里持有股票的最大利润。

**2. 确定遍历顺序**
股票是从第一天往后发展的，因此遍历顺序直接从第一天往后遍历即可。

**3. 确定递推公式**
``` java
dp[i][0] = max(dp[i-1][0],dp[i-1][1]+prices[i])
dp[i][1] = max(dp[i-1][0]-price[i],dp[i-1][1])
```
下面来解释一下，
dp[i][1]表示第i天结束以后手里存在股票，那么当时手里有股票可以由两个方面推出来：1. 昨天手里就有股票 2. 昨天手里没有股票，今天买的  这两种情况分别对应<code>dp[i-1][1]</code>和<code>dp[i-1][0]-prices[i]</code>，<code>dp[i-1][0]-prices[i]</code>的意思就是昨天手里没有股票，但是今天买了，因此手里持有的钱就少了那么多。
dp[i][0]表示第i天结束以后手里没有股票，那么当前手里没有股票可以由两个方面推出来：1. 昨天手里就没有股票 2. 昨天手里有股票，但是今天卖了。 这两种情况分别对应<code>dp[i-1][0] </code>和<code>dp[i-1][1]+prices[i] </code>后面<code>+prices[i]</code>的意思是今天卖股票的价格，卖了的钱算是赚的（因为在买入的时候已经已经减了买入时候的价格），因此<code>dp[i-1][1]+prices[i]</code>便是总的赚的钱。


**确定遍历顺序**

从前往后遍历即可

**推举dp数组**

手动推举可知，最后的结果是最后一天中卖或不卖数字最大的一个。


## 代码实现
### Java
``` java
class Solution {
    public int maxProfit(int[] prices) {
        int dp[][] = new int[prices.length][2];
        // dp[i][0] 表示第i天交易完成后手里没有股票的情况
        // dp[i][1] 表示第i天交易完成后手里有股票的情况
        dp[0][0] = 0;
        dp[0][1] = -prices[0];
        for(int i=1;i<prices.length;i++){
            dp[i][0] = Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
            dp[i][1] = Math.max(dp[i-1][0]-prices[i],dp[i-1][1]);
        }
        return dp[prices.length-1][0];
    }
}
```

### Go
``` Go
func maxProfit(prices []int) int {
   if len(prices) == 1{
        return 0
    }
    dp := make([][]int,len(prices))
    for i:=0;i<len(dp);i++{
        dp[i] = make([]int,2)
    }
    dp[0][0] = -prices[0]
    dp[0][1] = 0
    for i:= 1;i<len(prices);i++{
        // 是否当天买入，上一天买入的利润和当天买入的利润
        dp[i][0] = max(dp[i-1][0],dp[i-1][1]-prices[i])
        dp[i][1] = max(dp[i-1][1],dp[i-1][0]+prices[i])
    }
    return dp[len(prices)-1][1]
}

func max(a,b int) int{
    if a>b{
        return a
    }
    return b
}
```