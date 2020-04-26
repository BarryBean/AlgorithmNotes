# 总纲
核心思想是把问题抽象成图，从一个点开始，向四周扩散。一般用来在图中找 start 到 target 的最短距离。

具体实现都需要队列做辅助，再用一个数组存储访问过的节点。


# 模板
```java
int BFS(Node start, Node target) {
    Queue<Node> queue = new LinkedList<>();
    Set<Node> visited = new HashSet<>();
    // 将起点加入队列
    q.offer(start); 
    visited.add(start);
    // 记录扩散的步数
    int step = 0; 
    while (!queue.isEmpty()) {
        int size = queue.size();
        // 将当前队列中的所有节点向四周扩散 
        for (int i = 0; i < size; i++) {
            Node cur = queue.poll();
            // 判断是否到达终点
            if (cur is target){
                return step;
            }
            // 将 cur 的相邻节点加入队列
            // 一般是上下左右，用dx[] dy[] 实现
            for (Node x : cur.adj())
                if (!visited.contains(x)) {
                    queue.offer(x);
                    visited.add(x);
                }
        }
        // 更新步数
        step++;
    }
}
```

# 双向BFS
顾名思义，就是从起点和终点同时扩散，当两者相遇就停止。使用前提是知道终点在哪。

和单向BFS的思想区别在于：
- 用Set集合代替队列，以快速判断元素是否存在；
- 在遍历过程中，建立tmp存储扩散结果；
- q1 q2 一次遍历后互换，达到双向扩散的目的。

优化：
- 每次挑选较小的集合进行扩散，即若 q1<q2 => q1=tmp；反之 q2=tmp。

具体问题业务代码具体分析。
```java
int bfs(Node start, Node target) {
    // 用集合代替队列，快速判断元素是否存在
    Set<Node> q1 = new HashSet<>();
    Set<Node> q2 = new HashSet<>();
    Set<Node> visited = new HashSet<>();
    int step = 0;
    q1.add(start);
    q2.add(target);

    while (!q1.isEmpty() && !q2.isEmpty()) {
        step++;
        // 哈希集合在遍历的过程中不能修改，用 temp 存储扩散结果
        Set<Node> temp = new HashSet<>();
        
        for (Node cur : q1) {
            if (q2.contains(cur)){
                return step;
            }
            //visited.add(cur);
            for (Node x : cur.adj())
                if (!visited.contains(x)) {
                    temp.offer(x);
                    visited.add(x);
                }
            }
        }
        // temp 相当于 q1
        // 交换 q1 q2，下一轮 while 就是扩散 q2
        q1 = q2;
        q2 = temp;
    }
    return -1;
}
```


# 例题

| 题号 | 题目                                                         | 难度 |
| ---- | ------------------------------------------------------------ | ---- |
| 127  | [单词接龙](https://github.com/BarryBean/AlgorithmNotes/blob/master/BFS/127单词接龙.md) | 中等 |
| 994  | [腐烂的橘子](https://github.com/BarryBean/AlgorithmNotes/blob/master/BFS/994腐烂的橘子.md) | 中等 |
| 1162 | [地图分析](https://github.com/BarryBean/AlgorithmNotes/blob/master/BFS/1162地图分析.md) | 中等 |
| 752  | [打开转盘锁](https://github.com/BarryBean/AlgorithmNotes/blob/master/BFS/752打开转盘锁.md) | 中等 |
| 542  | [01矩阵](https://github.com/BarryBean/AlgorithmNotes/blob/master/BFS/542 01矩阵.md) | 中等 |

