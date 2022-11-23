# 剑指 Offer II 091. 粉刷房子

<a href="https://leetcode.cn/problems/JEj789/">leetcode链接</a><br>

假如有一排房子，共 n 个，每个房子可以被粉刷成红色、蓝色或者绿色这三种颜色中的一种，你需要粉刷所有的房子并且使其相邻的两个房子颜色不能相同。

当然，因为市场上不同颜色油漆的价格不同，所以房子粉刷成不同颜色的花费成本也是不同的。每个房子粉刷成不同颜色的花费是以一个 n x 3 的正整数矩阵 costs 来表示的。

例如，costs[0][0] 表示第 0 号房子粉刷成红色的成本花费；costs[1][2] 表示第 1 号房子粉刷成绿色的花费，以此类推。

请计算出粉刷完所有房子最少的花费成本。

**示例 1：** 
```text
输入: costs = [[17,2,17],[16,16,5],[14,3,19]]
输出: 10
解释: 将 0 号房子粉刷成蓝色，1 号房子粉刷成绿色，2 号房子粉刷成蓝色。
     最少花费: 2 + 5 + 3 = 10。
```
**示例 2：**
```
输入: costs = [[7,6,2]]
输出: 2
```
**提示:**

costs.length == n<br>
costs[i].length == 3<br>
1 <= n <= 100<br>
1 <= costs[i][j] <= 20<br>
**注意：**本题与主站 256 题相同：https://leetcode-cn.com/problems/paint-house/

Related Topics<br>
数组<br>
动态规划<br>
## 思路
我们想要找到最小的花费来粉刷所有的房子并且使其相邻的两个房子颜色不能相同，所以上述当前粉刷的房子颜色依赖上一个房子粉刷的颜色，可以采用动态规划。<br>
当做到动态规划题的时候，我们应该首先想到动态规划五部曲：

1. 确定dp数组（dp table）以及下标的含义
2. 确定递推公式
3. dp数组如何初始化
4. 确定遍历顺序
5. 举例推导dp数组

**确定dp数组下标及含义**

首先来确定dp数组的下标及含义，我们可以设dp[i][j]为第i行（第i个房子）粉刷第j个颜色所带来的总代价。（总代价就包含了前面房子的代价）

**确定递推公式**

在确定了dp数组下标和含义之后就可以确定递推公式了，当前房子所粉刷的颜色已经确定了，那么就往前推上一个房子可以粉刷的颜色。
如果当前房子粉刷的颜色是1，那么上一个房子能够粉刷的颜色便是0和2，因此便可以得到递推公式
``` java
    dp[i][0] = min(dp[i-1][1],dp[i-1][2])+costs[i][0]
    dp[i][1] = min(dp[i-1][0],dp[i-1][2])+costs[i][1]
    dp[i][2] = min(dp[i-1][1],dp[i-1][0])+costs[i][2]
```
**解释** 
当第i个房子粉刷0号颜色的时候，那么他的上一个房子可以粉刷第1、2号颜色，因此选取最小（题目要求）的一个代价色号进行粉刷。再加上粉刷当前颜色的代价。

**dp数组如何初始化**

这里，第一个房子不需要考虑前面的房子状态（因为前面没有房子），因此dp[0] = cost[0]即可。

**确定遍历顺序**

从前往后遍历即可

**推举dp数组**

手动推举可知，在最后一个房子所图的三种颜色代价中必有一个是最小的，这个最小的便是此题的解。因此dp数组遍历完成之后只需要获取dp[costs.length]这个数组中的最小值即可。


## 代码实现
 ### java
 ```
class Solution {
    public int minCost(int[][] costs) {
        if(costs == null || costs.length == 0){
            return 0;
        }
        int[][] dp = new int[costs.length][costs[0].length];
        dp[0] = costs[0].clone();
        for(int i =1;i<dp.length;i++){
           dp[i][0] = Math.min(dp[i-1][1],dp[i-1][2])+ costs[i][0];
           dp[i][1] = Math.min(dp[i-1][0],dp[i-1][2])+costs[i][1];
           dp[i][2] = Math.min(dp[i-1][0],dp[i-1][1])+costs[i][2];
        }
        return getMin(dp[dp.length-1]);
    }
    public int getMin(int[] arr){
        int min = arr[0];
        for(int i=0;i<arr.length;i++){
            if(arr[i]<min){
                min = arr[i];
            }
        }
        return min;
    }
}
 ```

