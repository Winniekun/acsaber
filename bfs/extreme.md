## [AC 844. 走迷宫](https://www.acwing.com/problem/content/846/)

给定一个 $n \times m$的二维整数数组，用来表示一个迷宫，数组中只包含 $0$ 或 $1$，其中 $0$ 表示可以走的路，$1$表示不可通过的墙壁。

最初，有一个人位于左上角 $(1,1)$ 处，已知该人每次可以向上、下、左、右任意一个方向移动一个位置。

请问，该人从左上角移动至右下角 $(n,m)$ 处，至少需要移动多少次。

数据保证 $(1,1)$ 处和 $(n,m)$ 处的数字为 $0$，且一定至少存在一条通路。

### 思路：

BFS，因为是一层一层的进行遍历可访问的位置，所以第一次遍历到$(n, m)$时，就是最少移动的次数。同时使用一个二维数组，访问每个位置的最短路径。

### 题解：

```java
import java.util.*;
public class Main {
    private static int[][] steps = {{1, 0}, {-1, 0}, {0, -1}, {0, 1}};
    private static int rows;
    private static int cols;
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        rows = sc.nextInt();
        cols = sc.nextInt();
        int[][] martrix = new int[rows][cols];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                martrix[i][j] = sc.nextInt();
            }
        }
        cal(martrix, rows, cols);
    }
    
    private static void cal(int[][] martrix, int rows, int cols) {
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{0, 0});
        int m = rows - 1;
        int n = cols - 1;
        int[] cur;
        int[][] dp = new int[rows][cols];
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                cur = queue.poll();
                for (int[] step : steps) {
                    int x = cur[0] + step[0];
                    int y = cur[1] + step[1];
                    if (isPos(x, y) && dp[x][y] == 0 && martrix[x][y] == 0) {
                        queue.offer(new int[]{x, y});
                        dp[x][y] = dp[cur[0]][cur[1]] + 1;
                    }
                }
            }
        }
        System.out.println(dp[m][n]);
    }
    
    private static boolean isPos(int x, int y) {
        return (x >= 0 && x < rows) && (y >= 0 && y < cols);
    }
}
```



## [AC 845. 八数码](https://www.acwing.com/problem/content/847/)

在一个 $3 \times 3$ 的网格中，$1 - 8$ 这 8 个数字和一个 `x` 恰好不重不漏地分布在这$3 \times 3$的网格中。

例如：

```
1 2 3
x 4 6
7 5 8
```

在游戏过程中，可以把 `x` 与其上、下、左、右四个方向之一的数字交换（如果存在）。我们的目的是通过交换，使得网格变为如下排列（称为正确排列）：

```
1 2 3
4 5 6
7 8 x
```

例如，示例中图形就可以通过让 `x` 先后与右、下、右三个方向的数字交换成功得到正确排列。交换过程如下：

```
1 2 3   1 2 3   1 2 3   1 2 3
x 4 6   4 x 6   4 5 6   4 5 6
7 5 8   7 5 8   7 x 8   7 8 x
```

现在，给你一个初始网格，请你求出得到正确排列至少需要进行多少次交换。

### 思路

每次都有`上`、`下`、`左`、`右`四个方向，每次交换之后，判断交换之后的二维数组和目标数组是否一致即可。

1. 为了方便进行比较，将二维数组转为一维的字符串，每次转换时，通过`cur.indexOf(x)`获取当前`x`所在的位置$idx$，之后idx / rows  和 idx % rows获得 在原二维数组的位置，然后`上`、`下`、`左`、`右`交换位置
2. 使用map记录转变为每个字符的最少次数 `key： str`，`value: count`。
3. 在BFS过程中，如果当前弹出的元素`cur == target`，返回map中存的次数即可

### 题解

```java
import java.util.*;
public class Main {
    private static int[][] steps = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        StringBuilder sb = new StringBuilder();
        String s = sc.nextLine();
        cal(s);
    }

    private static void cal(String s) {
        char[] array = s.toCharArray();
        StringBuilder sb = new StringBuilder();
        for (char c : array) {
            if (Character.isLetterOrDigit(c)) {
                sb.append(c);
            }
        }
        Queue<String> queue = new LinkedList<>();
        String init = sb.toString();
        String target = "12345678x";
        queue.offer(init);
        Map<String, Integer> map = new HashMap<>();
        map.put(init, 0);
        boolean flag = false;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                String cur = queue.poll();
                if (cur.equals(target)) {
                    System.out.println(map.get(cur));
                    flag = true;
                    break;
                }
                int pos = cur.indexOf('x');
                // 转化为当前的二维位置
                int x = pos / 3;
                int y = pos % 3;
                for (int[] step : steps) {
                    int nx = x + step[0];
                    int ny = y + step[1];
                    if (!isPos(nx, ny)) {
                        continue;
                    }
                    String nextStr = makeStr(cur, pos, nx * 3 + ny);
                    if (map.containsKey(nextStr)) { // 只记录第一出现的时候，保证最短路径
                        continue;
                    }
                    map.put(nextStr, map.get(cur) + 1);
                    queue.offer(nextStr);
                }

            }
        }
        if (!flag) {
            System.out.println(-1);
        }
    }

    private static boolean isPos(int x, int y) {
        return (x >= 0 && x < 3) && (y >= 0 && y < 3);
    }

    private static String makeStr(String str, int src, int dist) {
        StringBuilder sb = new StringBuilder(str);
        sb.setCharAt(dist, 'x');
        sb.setCharAt(src, str.charAt(dist));
        return sb.toString();
    }
}
```

## [LC 773. 滑动谜题](https://leetcode-cn.com/problems/sliding-puzzle/)

在一个 2 x 3 的板上（board）有 5 块砖瓦，用数字 1~5 来表示, 以及一块空缺用 0 来表示.

一次移动定义为选择 0 与一个相邻的数字（上下左右）进行交换.

最终当板 board 的结果是 [[1,2,3],[4,5,0]] 谜板被解开。

给出一个谜板的初始状态，返回最少可以通过多少次移动解开谜板，如果不能解开谜板，则返回 -1 。

### 思路

整体思路和`八数码`一致

### 题解

```java
class Solution {
    int[][] steps = {{-1, 0}, {1, 0}, {0, 1}, {0, -1}};

    public int slidingPuzzle(int[][] board) {
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < 2; ++i) {
            for (int j = 0; j < 3; ++j) {
                sb.append(board[i][j]);
            }
        }
        String target = "123450";
        String initial = sb.toString();
        if (target.equals(initial)) {
            return 0;
        }

        Queue<String> queue = new LinkedList<>();
        queue.offer(initial);
        Map<String, Integer> map = new HashMap<>();
        map.put(initial, 0);
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; ++i) {
                String curStatus = queue.poll();
                if (curStatus.equals(target)) {
                    return map.get(curStatus);
                }
                int pos = curStatus.indexOf("0");
                int x = pos / 3;
                int y = pos % 3;
                for (int[] dis : steps) {
                    int nx = x + dis[0];
                    int ny = y + dis[1];
                    if (!isPow(nx, ny)) {
                        continue;
                    }
                    String nextStatus = makeStr(curStatus, pos, nx * 3 + ny);
                    if (map.containsKey(nextStatus)) {
                        continue;
                    }
                    map.put(nextStatus, map.get(curStatus) + 1);
                    queue.offer(nextStatus);

                }
            }
        }
        return -1;
    }

    private boolean isPow(int x, int y) {
        return (x >= 0 && x < 2) && (y >= 0 && y < 3);
    }

    private String makeStr(String t, int src, int dest) {
        StringBuilder sb = new StringBuilder(t);
        sb.setCharAt(src, t.charAt(dest));
        sb.setCharAt(dest, '0');
        return sb.toString();
    }
}
```

