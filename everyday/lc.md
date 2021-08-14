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



