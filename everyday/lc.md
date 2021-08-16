## 2021年8月

### [1583. 统计不开心的朋友](https://leetcode-cn.com/problems/count-unhappy-friends/)

**题目：**

```
给你一份 n 位朋友的亲近程度列表，其中 n 总是 偶数 。

对每位朋友 i，preferences[i] 包含一份 按亲近程度从高到低排列 的朋友列表。换句话说，排在列表前面的朋友与 i 的亲近程度比排在列表后面的朋友更高。每个列表中的朋友均以 0 到 n-1 之间的整数表示。

所有的朋友被分成几对，配对情况以列表 pairs 给出，其中 pairs[i] = [xi, yi] 表示 xi 与 yi 配对，且 yi 与 xi 配对。

但是，这样的配对情况可能会是其中部分朋友感到不开心。在 x 与 y 配对且 u 与 v 配对的情况下，如果同时满足下述两个条件，x 就会不开心：

x 与 u 的亲近程度胜过 x 与 y，且
u 与 x 的亲近程度胜过 u 与 v
返回 不开心的朋友的数目 。
```

**思路：** 暴力模拟

对于每个`x`，先找到和他组队的人`y`，寻找`x` 的亲近程度列表中有没有比 `y` 更高的，如果有，再看这些人 `u`与 `x` 的亲密程度是否比跟他们组队的人 `v` 更高，如果是，则 `x` 就是不开心的。

因为，在这个过程中，我们需要快速地找到与`u` 配对的` v` ，以及`u`与`x`的亲密程度是否比` v` 高，所以，我们要先整理数据，保存每个人的配对情况及亲密程度情况，一般来说，我们使用哈希表是可以的，但是，本题限定了范围是` 0~n-1`，所以，我们直接使用两个数组即可。

**题解：**

```java
class Solution {
    public int unhappyFriends(int n, int[][] preferences, int[][] pairs) {
        // close[i][j]表示 i 和 j 的亲密程度
        int[][] close = new int[n][n];
        // teammate[i]表示 i 的队友是谁
        int[] teammate = new int[n];
        // 整理亲密程度
        for (int i = 0; i < preferences.length; i++) {
            for (int j = 0; j < preferences[i].length; j++) {
                // 数值越小越亲近
                close[i][preferences[i][j]] = j;
            }
        }
        // 整理配对情况
        for (int[] pair : pairs) {
            teammate[pair[0]] = pair[1];
            teammate[pair[1]] = pair[0];
        }

        // 使用set一次可以找到两个人
        // 但是运行时间更慢了
        // 所以我们不是一个一个找
        int ans = 0;

        // 遍历所有的n个人
        for (int x = 0; x < n; x++) {
            // 找到 x 的队友 y
            int y = teammate[x];
            // 看看有没有亲密程度排在y之前的，即可能的u
            for (int u : preferences[x]) {
                if (u == y) {
                    break;
                }
                // 在y之前找到一个可能的u
                // 看u与x的亲密程度是否大于u的队友v
                int v = teammate[u];
                if (close[u][x] < close[u][v]) {
                    ans++;
                    // x已经确定不开心了，寻找下一个吧
                    break;
                }
            }
        }

        return ans;
    }
}
```



### [526. 优美的排列](https://leetcode-cn.com/problems/beautiful-arrangement/)

**题目：**

```java
假设有从 1 到 N 的 N 个整数，如果从这 N 个数字中成功构造出一个数组，使得数组的第 i 位 (1 <= i <= N) 满足如下两个条件中的一个，我们就称这个数组为一个优美的排列。条件：

第 i 位的数字能被 i 整除
i 能被第 i 位上的数字整除
现在给定一个整数 N，请问可以构造多少个优美的排列？

N < 16
```

**思路：**

1. 求所有的可能性，最先想到的是加回溯的dfs，题目直白理解：N的数字放入到N个槽位，然后并且要符合优美排列规则
   1. 从位置1开始，每个位置都有都有n中可能，然后一次递归需要标记元素是否访问，保持递归内数据一致，使用一个boolean[] 数组记录
   2. 根据上述，得出入参两个，当前所在位置，标记数组，最后再添加一个长度，用于做终止条件判断

**题解：**

```java
class Solution {
    int res = 0;
    public int countArrangement(int N) {
        boolean[] flag = new boolean[N + 1];
        helper(flag, N, 1);
        return res;
    }

    private void helper(boolean[] flag, int N, int count) {
        if (count == N + 1) {
            res++;
            return;
        }
        for (int i = 1; i <= N; i++) {
            // 已经被使用了
            if (flag[i]) continue;
            //剪枝条件：如果不能被i或整除i
            if (!judge(count, i)) continue;
            flag[i] = true;
            helper(flag, N, count + 1);
            flag[i] = false;
        }
    }

    private boolean judge(int num, int idx) {
        return (num % idx == 0) || (idx % num == 0);
    }
}
```

