## [AC 846. 树的重心](https://www.acwing.com/problem/content/848/)

**题目：**

```tex
给定一颗树，树中包含 n 个结点（编号 1∼n）和 n−1 条无向边。

请你找到树的重心，并输出将重心删除后，剩余各个连通块中点数的最大值。

重心定义：重心是指树中的一个结点，如果将这个点删除后，剩余各个连通块中点数的最大值最小，那么这个节点被称为树的重心。

输入格式
第一行包含整数 n，表示树的结点数。

接下来 n−1 行，每行包含两个整数 a 和 b，表示点 a 和点 b 之间存在一条边。

输出格式
输出一个整数 m，表示将重心删除后，剩余各个连通块中点数的最大值。

数据范围
1≤n≤105
```

**思路：**

整体的思路就是，使用dfs遍历，通过dfs就能够获取当前节点对应的子树中树节点的个数，删除当前节点，得到两种子树

1. 其孩子节点对应的子树，如下的蓝色和小绿色
2. 剩余节点构成的子树（节点个数 $n - 当前节点为根节点的子树节点数量$）

所以，为了获取最大值的最小值，我们可以遍历每个节点，然后使用一个全局变量ans分别和上述的所有子树节点比较即可。其实说白了就是后序遍历一颗树。然后确定好需要向上传递什么值，然后如何计算当前的值，和整体的最优结果做对比。

![image-20210819234620029](https://cdn.jsdelivr.net/gh/Winniekun/cloudImg@master/uPic/image-20210819234620029.png)

**题解：**

```java
import java.util.*;

public class Main {
    private static int min = Integer.MAX_VALUE;
    private static boolean[] visited;
    private static int n;
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i <= n; i++) {
            adj.add(new ArrayList<>());
        }
        visited = new boolean[n + 1];
        for (int i = 0; i < n - 1; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            adj.get(a).add(b);
            adj.get(b).add(a);
        }
        dfs(adj, 1);
        System.out.println(min);
    }
  
    /**
     * 返回以cur为根的子树中节点的个数，包括cur节点
     */
    private static int dfs(List<List<Integer>> adj, int cur) {
        visited[cur] = true;
        int sum = 1;    //当前子树大小，包括自己故从1开始
        int res = 0;    //删除该结点后每一个连通块大小的最大值
        List<Integer> next = adj.get(cur);
        for (int num : next) {
            if (!visited[num]) {
                int s = dfs(adj, num);
                sum += s;
                res = Math.max(res, s);
            }
        }
        res = Math.max(res, n - sum);
        min = Math.min(min, res);
        return sum;
    }
}
```

