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

### [552. 学生出勤记录 II](https://leetcode-cn.com/problems/student-attendance-record-ii/)

**题目：**

```
可以用字符串表示一个学生的出勤记录，其中的每个字符用来标记当天的出勤情况（缺勤、迟到、到场）。记录中只含下面三种字符：
'A'：Absent，缺勤
'L'：Late，迟到
'P'：Present，到场
如果学生能够 同时 满足下面两个条件，则可以获得出勤奖励：
按总出勤计，学生缺勤（'A'）严格 少于两天。
学生不会存在连续3天或连续3天以上的迟到（'L'）记录。
给你一个整数 n ，表示出勤记录的长度（次数）。请你返回记录长度为 n 时，可能获得出勤奖励的记录情况 数量 。答案可能很大，所以返回对 109 + 7 取余 的结果。

示例 1：
> 输入：n = 2
> 输出：8
> 解释：
> 有 8 种长度为 2 的记录将被视为可奖励：
> "PP" , "AP", "PA", "LP", "PL", "AL", "LA", "LL" 
> 只有"AA"不会被视为可奖励，因为缺勤次数为 2 次（需要少于 2 次）。

示例 2：
> 输入：n = 1
> 输出：3

示例 3：
> 输入：n = 10101
> 输出：183236316
```

**思路：**

趁着这道题，整理下怎么通过dfs一步一步进化到动归的思路吧

1. dfs
   1. 因为求所有的结果，根据题目的意思就是， 将`P`、`L`
2. 记忆化dfs
3. 根据dfs定义以及入参和计算方式，延伸出动归的定义，以及状态方程
4. 优化 滚动数组。。。 不写了

**题解：**

```java
// dfs
public int checkRecordI(int n) {
    return dfsI(0, n, 0, 0);
}
private int dfsI(int day, int n, int absent, int late) {
    if (day >= n) {
        return 1;
    }
    int ans = 0;
    ans = (ans + dfsI(day + 1, n, absent, 0)) % MOD;
    if (absent < 1) {
        ans = (ans + dfsI(day + 1, n, 1, 0)) % MOD;
    }
    if (late < 2) {
        ans = (ans + dfsI(day + 1, n, absent, late + 1)) % MOD;
    }
    return ans;
}

// dfs + 记忆化
public int checkRecord(int n) {
    int[][][] memo = new int[n][2][3];
    return dfs(0, n, 0, 0, memo);
}
private int dfs(int day, int n, int absent, int late, int[][][] memo) {
    if (day >= n) {
        return 1;
    }
    // 相同的状态计算过了直接返回
    if (memo[day][absent][late] != 0) {
        return memo[day][absent][late];
    }
    int ans = 0;
    // present 随意放
    ans = (ans + dfs(day + 1, n, absent, 0, memo)) % MOD;
    // absent 只能放1个
    if (absent < 1) {
        ans = (ans + dfs(day + 1, n, 1, 0, memo)) % MOD;
    }
    // late 最多放连续的两个
    if (late < 2) {
        ans = (ans + dfs(day + 1, n, absent, late + 1, memo)) % MOD;
    }
    // 记录每一个状态的计算结果
    memo[day][absent][late] = ans;
    return ans;
}

// 动态规划
public int checkRecordII(int n) {
    long[][][] dp = new long[n][2][3];
    // 初始值
    dp[0][0][0] = 1;
    dp[0][1][0] = 1;
    dp[0][0][1] = 1;
    for (int i = 1; i < n; i++) {
        // 本次填入P，分成前一天累计了0个A和1个A两种情况
        dp[i][0][0] = (dp[i - 1][0][0] + dp[i - 1][0][1] + dp[i - 1][0][2]) % MOD;
        dp[i][1][0] = (dp[i - 1][1][0] + dp[i - 1][1][1] + dp[i - 1][1][2]) % MOD;
        // 本次填入A，前一天没有累计A都能转移过来
        // 这行可以与上面一行合并计算，为了方便理解，我们分开，下面会合并
        dp[i][1][0] = (dp[i][1][0] + dp[i - 1][0][0] + dp[i - 1][0][1] + dp[i - 1][0][2]) % MOD;
        // 本次填入L，前一天最多只有一个连续的L，分成四种情况
        dp[i][0][1] = dp[i - 1][0][0];
        dp[i][0][2] = dp[i - 1][0][1];
        dp[i][1][1] = dp[i - 1][1][0];
        dp[i][1][2] = dp[i - 1][1][1];
    }
    // 计算结果，即最后一天的所有状态相加
    long ans = 0;
    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 3; j++) {
            ans = (ans + dp[n - 1][i][j]) % MOD;
        }
    }
    return (int) ans;
}
```

### [541. 反转字符串 II](https://leetcode-cn.com/problems/reverse-string-ii/)

**题目：**

```tex
给定一个字符串 s 和一个整数 k，从字符串开头算起，每 2k 个字符反转前 k 个字符。

如果剩余字符少于 k 个，则将剩余字符全部反转。
如果剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符，其余字符保持原样。
```

**思路：**

字符串反转，基本功。

根据题意：每$2k$个字符，交换前$k$个字符。所以使用双指针解决，然后需要注意更新双指针的方式。

**题解：**

```java
class Solution {
    public String reverseStr(String s, int k) {
        char[] arr = s.toCharArray();
        int i = 0;
        int j = k - 1;
        int n = arr.length;
        int times = n / (2 * k);
        while (times-- > 0) {
            exchange(arr, i, j);
            i = i + 2 * k;
            j = i + k - 1 >= n ? n - 1 : i + k - 1;
        }
        int rst = n % (2 * k);
        if (rst < k) {
            exchange(arr, i,  i + rst - 1);
        } else {
            exchange(arr, i, i + k - 1);
        }
        return new String(arr);
    }

    private void exchange(char[] arr, int left, int right) {
        while (left < right) {
            swap(arr, left, right);
            left++;
            right--;
        }
    }

    private void swap(char[] arr, int i, int j) {
        char tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }
}
```

