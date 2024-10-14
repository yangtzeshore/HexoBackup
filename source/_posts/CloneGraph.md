---
title: CloneGraph
date: 2021-12-25 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### [克隆图](https://leetcode-cn.com/problems/clone-graph/)

- [题目介绍](https://yangtzeshore.github.io/2021/12/25/CloneGraph/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/12/25/CloneGraph/#题目解法)

### 题目介绍

#### 克隆图

给你无向 **[连通](https://baike.baidu.com/item/连通图/6460995?fr=aladdin)** 图中一个节点的引用，请你返回该图的 [**深拷贝**](https://baike.baidu.com/item/深拷贝/22785317?fr=aladdin)（克隆）。

图中的每个节点都包含它的值 `val`（`int`） 和其邻居的列表（`list[Node]`）。

```
class Node {
    public int val;
    public List<Node> neighbors;
}
```

**测试用例格式：**

简单起见，每个节点的值都和它的索引相同。例如，第一个节点值为 1（`val = 1`），第二个节点值为 2（`val = 2`），以此类推。该图在测试用例中使用邻接列表表示。

**邻接列表** 是用于表示有限图的无序列表的集合。每个列表都描述了图中节点的邻居集。

给定节点将始终是图中的第一个节点（值为 1）。你必须将 **给定节点的拷贝** 作为对克隆图的引用返回。

**示例 1:**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/133_clone_graph_question.png)

```
输入：adjList = [[2,4],[1,3],[2,4],[1,3]]
输出：[[2,4],[1,3],[2,4],[1,3]]
解释：
图中有 4 个节点。
节点 1 的值是 1，它有两个邻居：节点 2 和 4 。
节点 2 的值是 2，它有两个邻居：节点 1 和 3 。
节点 3 的值是 3，它有两个邻居：节点 2 和 4 。
节点 4 的值是 4，它有两个邻居：节点 1 和 3 。
```

**示例 2:**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/graph.png)

```
输入：adjList = [[]]
输出：[[]]
解释：输入包含一个空列表。该图仅仅只有一个值为 1 的节点，它没有任何邻居。
```

**示例 3：**

```
输入：adjList = []
输出：[]
解释：这个图是空的，它不含任何节点。
```

**示例 4：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/graph-1.png)

```
输入：adjList = [[2],[1]]
输出：[[2],[1]]
```

**提示：**

1. 节点数不超过 100 。
2. 每个节点值 `Node.val` 都是唯一的，`1 <= Node.val <= 100`。
3. 无向图是一个[简单图](https://baike.baidu.com/item/简单图/1680528?fr=aladdin)，这意味着图中没有重复的边，也没有自环。
4. 由于图是无向的，如果节点 *p* 是节点 *q* 的邻居，那么节点 *q* 也必须是节点 *p* 的邻居。
5. 图是连通图，你可以从给定节点访问到所有节点。

### 题目解法

```
package algorithm;

import java.util.*;

public class CloneGraph {

    public Node cloneGraph(Node node) {
        Set<Integer> set = new HashSet<>();
        Node dfsNode = dfs(node, set);
        node.neighbors = dfsNode.neighbors;
        return dfsNode;
    }

    private Node dfs(Node node, Set<Integer> set) {
        if (node == null) {
            return null;
        }
        List<Node> neighbors = node.neighbors;
        Node nodeNew = new Node(node.val);
        for (Node neighbor : neighbors) {
            if (set.contains(neighbor.val)) {
                continue;
            }
            set.add(neighbor.val);
            Node nextNode = dfs(neighbor, set);
            if (nodeNew.neighbors == null) {
                List<Node> list = new ArrayList<>();
                list.add(nextNode);
                nodeNew.neighbors = list;
            } else {
                nodeNew.neighbors.add(nodeNew);
            }
        }
        return nodeNew;
    }

    public static void main(String[] args) {
        CloneGraph s = new CloneGraph();
        Node node = null;
        Node result = s.cloneGraph(node);
        System.out.println(result);

        node = new Node(1);
        result = s.cloneGraph(node);
        System.out.println(result);
    }

    private static void print(Node node) {
        System.out.println(node.val);
    }


    static class Node {
        public int val;
        public List<Node> neighbors;
        public Node() {
            val = 0;
            neighbors = new ArrayList<Node>();
        }
        public Node(int _val) {
            val = _val;
            neighbors = new ArrayList<Node>();
        }
        public Node(int _val, ArrayList<Node> _neighbors) {
            val = _val;
            neighbors = _neighbors;
        }
    }
}
```

打印：

```

```

**思路：**

思路上， 其实并不难，需要注意遍历然后记录遍历，还有就是返回遍历的克隆节点。如果是1-2这种，1的邻居当然是2，2的邻居当然也是1，需要注意将已经遍历收集到的邻居直接返回，不需要再次遍历邻居。好久没有做算法题，终于不忙了。