# BFS

广度优先搜索（BFS）是连通图的一种遍历策略，顾名思义，其与深度优先搜索相对；

试想一下这个问题：单点最短路径，给定一幅图和一个起点s，回答从s到给定目的的顶点v是否存在一条路径，如果有，找出其中最短的那条（所含边数最少）等类似的问题；

解决这个问题的方法叫做广度优先搜索；

具体解决问题的方式是，使用一个队列来保存所有已经被标记过但其邻接表还未被检查过的点。先将起点加入队列，然后再执行下面的步骤：



+ 获取当前队列size，循环i从0-size

	+ 拉取当前队首元素，将当前队首元素所邻接的、未被访问过的元素加入队尾；

	+ 判断当前元素是否是解（目标点）
		+ 如果是就直接返回
		+ 不是则continue
	+ bfs层数加一



## 代码框架

```java
// 计算从起点 start 到终点 target 的最近距离
int BFS(Node start, Node target) {
    Queue<Node> q; // 核心数据结构
    Set<Node> visited; // 避免走回头路
    
    q.offer(start); // 将起点加入队列
    visited.add(start);
    int step = 0; // 记录扩散的步数

    while (q not empty) {
        int sz = q.size();
        /* 将当前队列中的所有节点向四周扩散 */
        for (int i = 0; i < sz; i++) {
            Node cur = q.poll();
            /* 划重点：这里判断是否到达终点 */
            if (cur is target)
                return step;
            /* 将 cur 的相邻节点加入队列 */
            for (Node x : cur.adj()) {
                if (x not in visited) {
                    q.offer(x);
                    visited.add(x);
                }
            }
        }
        /* 划重点：更新步数在这里 */
        step++;
    }
}
```

