# [雇佣 K 位工人的总代价](https://leetcode.cn/problems/total-cost-to-hire-k-workers/)

[雇佣 K 位工人的总代价](https://leetcode.cn/problems/total-cost-to-hire-k-workers/)



给你一个下标从 **0** 开始的整数数组 `costs` ，其中 `costs[i]` 是雇佣第 `i` 位工人的代价。

同时给你两个整数 `k` 和 `candidates` 。我们想根据以下规则恰好雇佣 `k` 位工人：

+ 总共进行 `k` 轮雇佣，且每一轮恰好雇佣一位工人。
+ 在每一轮雇佣中，从最前面 `candidates` 和最后面 `candidates` 人中选出代价最小的一位工人，如果有多位代价相同且最小的工人，选择下标更小的一位工人。
	+ 比方说，`costs = [3,2,7,7,1,2] `且` candidates = 2` ，第一轮雇佣中，我们选择第 4 位工人，因为他的代价最小 `[3,2,7,7,1,2] `。
	+ 第二轮雇佣，我们选择第 1 位工人，因为他们的代价与第 4 位工人一样都是最小代价，而且下标更小，`[3,2,7,7,2]` 。注意每一轮雇佣后，剩余工人的下标可能会发生变化。
+ 如果剩余员工数目不足 `candidates` 人，那么下一轮雇佣他们中代价最小的一人，如果有多位代价相同且最小的工人，选择下标更小的一位工人。
+ 一位工人只能被选择一次。

返回雇佣恰好 `k` 位工人的总代价。



**示例 1：**

```
输入：costs = [17,12,10,2,7,2,11,20,8], k = 3, candidates = 4
输出：11
解释：我们总共雇佣 3 位工人。总代价一开始为 0 。
- 第一轮雇佣，我们从 [17,12,10,2,7,2,11,20,8] 中选择。最小代价是 2 ，有两位工人，我们选择下标更小的一位工人，即第 3 位工人。总代价是 0 + 2 = 2 。
- 第二轮雇佣，我们从 [17,12,10,7,2,11,20,8] 中选择。最小代价是 2 ，下标为 4 ，总代价是 2 + 2 = 4 。
- 第三轮雇佣，我们从 [17,12,10,7,11,20,8] 中选择，最小代价是 7 ，下标为 3 ，总代价是 4 + 7 = 11 。注意下标为 3 的工人同时在最前面和最后面 4 位工人中。
总雇佣代价是 11 。
```

**示例 2：**

```
输入：costs = [1,2,4,1], k = 3, candidates = 3
输出：4
解释：我们总共雇佣 3 位工人。总代价一开始为 0 。
- 第一轮雇佣，我们从 [1,2,4,1] 中选择。最小代价为 1 ，有两位工人，我们选择下标更小的一位工人，即第 0 位工人，总代价是 0 + 1 = 1 。注意，下标为 1 和 2 的工人同时在最前面和最后面 3 位工人中。
- 第二轮雇佣，我们从 [2,4,1] 中选择。最小代价为 1 ，下标为 2 ，总代价是 1 + 1 = 2 。
- 第三轮雇佣，少于 3 位工人，我们从剩余工人 [2,4] 中选择。最小代价是 2 ，下标为 0 。总代价为 2 + 2 = 4 。
总雇佣代价是 4 。
```

**提示：**

- `1 <= costs.length <= 105`
- `1 <= costs[i] <= 105`
- `1 <= k, candidates <= costs.length`



## 思路

​		看到这道题，一般都能够想到一个比较基本的暴力破解法，直接每次都去遍历前面`candidates`个元素和后面`candidates`个元素，然后一共遍历k次即可。但是，这种方法时间复杂度较高，每次都需要进行遍历，即使当前区间没有变过。

​		为了解决这个问题，我们可以在第一次的时候进行一次遍历，然后保存当前区间的最小值，如果当前区间删除掉了最小值，那就维护到第二个最小值。意思就是我们可以一直维护前后两个区间的最小值，每次只需要比较这两个区间的最小值谁最小就将谁弹出区间即可。在现在有的数据结构中，我们可以使用**优先级队列**或者是**堆**来解决这个问题，维护两个优先级队列或者是两个小根堆，每次取两个队首/堆顶元素的最小值即可；

那么具体步骤可以这样：

+ 创建两个优先级队列
+ 将前candidates个元素加入第一个优先级队列，将后candidates个元素加入优先级队列
	+ 在加入队列的时候要注意两个队列是否有交集，如果两个队列有交集的话可以合并队列或者是交集部分只加入一次队列；
+ 依次遍历k次，每次记录两个队列的最小值，然后往前或者往后添加元素；



## 代码

### java

```java
class Solution {
    public long totalCost(int[] costs, int k, int candidates) {
        Long res = 0L;
        var first = new PriorityQueue<Integer>();
        var last = new PriorityQueue<Integer>();
        int pf = 0;
        int pl = 0;
        for (int i = 0; i < Math.min(costs.length, candidates); i++) {
            first.offer(costs[i]);
            pf = i;
        }
        for (int i = costs.length-1, count = 0; i >= candidates && count < candidates; i--, count++) {
            last.offer(costs[i]);
            pl = i;
        }
        for (int i = 0; i < k; i++) {

            int q = Integer.MAX_VALUE;
            int h = Integer.MAX_VALUE;
            if (!first.isEmpty()) {
                q = first.peek();
            }
            if (!last.isEmpty()) {
                h = last.peek();
            }
            res += Math.min(q, h);
            if (q <= h){
                 first.poll();
            }else{
                last.poll();
            }
            if (q <= h && pf+1 < pl && pf+1 < costs.length) {
                first.offer(costs[++pf]);
            } else if (pf < pl-1 && pl-1 >= 0) {
                last.offer(costs[--pl]);
            }
        }
        return res;
    }
}
```



