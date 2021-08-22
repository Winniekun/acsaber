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

### [789. 逃脱阻碍者](https://leetcode-cn.com/problems/escape-the-ghosts/)

**题目：**

```
你在进行一个简化版的吃豆人游戏。你从 [0, 0] 点开始出发，你的目的地是 target = [xtarget, ytarget] 。地图上有一些阻碍者，以数组 ghosts 给出，第 i 个阻碍者从 ghosts[i] = [xi, yi] 出发。所有输入均为 整数坐标 。

每一回合，你和阻碍者们可以同时向东，西，南，北四个方向移动，每次可以移动到距离原位置 1 个单位 的新位置。当然，也可以选择 不动 。所有动作 同时 发生。

如果你可以在任何阻碍者抓住你 之前 到达目的地（阻碍者可以采取任意行动方式），则被视为逃脱成功。如果你和阻碍者同时到达了一个位置（包括目的地）都不算是逃脱成功。

只有在你有可能成功逃脱时，输出 true ；否则，输出 false 。

示例 1：

输入：ghosts = [[1,0],[0,3]], target = [0,1]
输出：true
解释：你可以直接一步到达目的地 (0,1) ，在 (1, 0) 或者 (0, 3) 位置的阻碍者都不可能抓住你。 
```

**思路：**

理解题目，做起来就简单了。

![题目](https://cdn.jsdelivr.net/gh/Winniekun/cloudImg@master/uPic/image-20210822175220951.png)

以上图为例。玩家初始点在$P$, 然后两个阻碍者分别为$G1、G2$，$T1、T2、T3$分别为三个目的地。$T1$能够安全到达，$T2、T3$就有不行。题目中明确说了，只能上下左右移动。所以我们只需要计算$G_i$到$T$的曼哈顿距离$D_i$和$P$到$T$的曼哈顿距离$D_p$。

1. $Di < D_p$ 说明$G_i$会在比玩家更早到达终点$T$，所以$G_i$只需要在$T$等着就行，玩家无法逃脱
2. $D_i > D_p $ 说明所有的$G_i$距离终点$T$都比玩家距离终点$T$远。玩家能够成功逃脱

**曼哈顿距离**公式：$dist(p1, p2) = | x_1 - x_2 | + |y_1 - y_2 |$。

那么，会不会存在，如果$G_1$到目的地的距离确实比玩家到目的地的距离远（$dist(g, t) > dist(p, t)$），但是$G_i$能拦截到$P$ ？ 我们证明下：

假设拦截点为 X，看看会发生什么情况：

1. 玩家到目的地的距离：$dist(p,t) = dist(p, x) + dist(x, t)$
2. 鬼到目的地的距离：$dist(g,t) = dist(g, x) + dist(x, t)$ 
3. 能拦截到说明：$dist(g, x) < dist(p, x)$
4. 那么两边同时加上相同的值，有：$dist(g, x) + dist(x, t) < dist(p, x) + dist(x, t)$
5. 最后，得出：$dist(g, t) < dist(p, t)$
6. 这与题目我们假设的鬼到目的地的距离确实比玩家到目的地的距离远相悖了。

**题解：**

```java
class Solution {
    public boolean escapeGhosts(int[][] ghosts, int[] target) {
        int baseDist = genDistance(new int[]{0, 0}, target);
        for (int[] ghost : ghosts) {
            int innerDist = genDistance(ghost, target);
            if (innerDist <= baseDist) {
                return false;
            }
        }
        return true;
    }

    private int genDistance(int[] base, int[] target) {
        return (int)(Math.abs(base[0] - target[0]) + Math.abs(base[1] - target[1]));
    }
}
```

### [1646. 获取生成数组中的最大值](https://leetcode-cn.com/problems/get-maximum-in-generated-array/)

**题目：**

```
给你一个整数 n 。按下述规则生成一个长度为 n + 1 的数组 nums ：

nums[0] = 0
nums[1] = 1
当 2 <= 2 * i <= n 时，nums[2 * i] = nums[i]
当 2 <= 2 * i + 1 <= n 时，nums[2 * i + 1] = nums[i] + nums[i + 1]
返回生成数组 nums 中的 最大 值。
```

**思路：**

根据题意来就行了，递归、迭代、动归都能解决

动归的状态转移方程：

$\left\{ \begin{array}{rcl} i \% 2 == 0  && {dp[i] = dp[i / 2]}\\ i \% 2 == 1 &&{dp[i] = dp[i / 2] + dp[i / 2 + 1]}\end{array} \right. $

**题解：**

```java
// 模拟
class Solution {
    public int getMaximumGenerated(int n) {
        if (n < 2) {
            return n;
        }
        int[] arr = new int[n + 1];
        arr[0] = 0;
        arr[1] = 1;
        for (int i = 2; i <= n; i++) {
            genNum(i, arr);
        }
        return Arrays.stream(arr).max().getAsInt();
    }
    
    private void genNum(int idx, int[] arr) {
        int pre = 0;
        if (idx % 2 == 1) {
            pre = (idx - 1) / 2;
            arr[idx] = arr[pre] + arr[pre + 1]; 
        } else {
            pre = idx / 2;
            arr[idx] = arr[pre];
        }
    }
}

// 动归
class Solution {
    public static int getMaximumGenerated(int n) {
        if (n < 2) {
            return n;
        }
        int max = Integer.MIN_VALUE;
        int[] dp = new int[n + 1];
        dp[0] = 0;
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i / 2] + ((i % 2 == 0) ? 0 : dp[i / 2 + 1]);
            max = Math.max(dp[i], max);
        }
        return max;
    }
}
```

### [1109. 航班预订统计](https://leetcode-cn.com/problems/corporate-flight-bookings/)

**题目：**

```
这里有 n 个航班，它们分别从 1 到 n 进行编号。
有一份航班预订表 bookings ，表中第 i 条预订记录 bookings[i] = [firsti, lasti, seatsi] 意味着在从 firsti 到 lasti （包含 firsti 和 lasti ）的 每个航班 上预订了 seatsi 个座位。
请你返回一个长度为 n 的数组 answer，其中 answer[i] 是航班 i 上预订的座位总数。

示例 1：
输入：bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5
输出：[10,55,45,25,25]
解释：
航班编号        1   2   3   4   5
预订记录 1 ：   10  10
预订记录 2 ：       20  20
预订记录 3 ：       25  25  25  25
总座位数：      10  55  45  25  25
因此，answer = [10,55,45,25,25]

示例 2：
输入：bookings = [[1,2,10],[2,2,15]], n = 2
输出：[10,25]
解释：
航班编号        1   2
预订记录 1 ：   10  10
预订记录 2 ：       15
总座位数：      10  25
因此，answer = [10,25]

提示：
1 <= n <= 2 * 104
1 <= bookings.length <= 2 * 104
bookings[i].length == 3
1 <= firsti <= lasti <= n
1 <= seatsi <= 104
```

**思路：**

最开始的思路就是保留求解，时间复杂度$O(n^2)$，空间复杂度$O(n)$，数据量达到了$2 \times 10 ^4$ 会报TLE，所以需要优化到$O(n)$的时间复杂度的思路。**差分思路**解决：差分数组对应的概念是前缀和数组，对于数组$\ [1,2,2,4]$，其差分数组为 $[1,1,0,2]$，差分数组的第$i$个数即为原数组的第$i - 1$个元素和第$i$个元素的差值，也就是说我们对差分数组求前缀和即可得到原数组。

同时差分数组的性质是，当我们希望对原数组的某一个区间 $[l,r]$ 施加一个增量$inc$ 时，差分数组$d$对应的改变是

1. $d[l] + inc$
2. $d[r + 1] - inc$

**题解：**

```java
class Solution {
   // 差分思路
   public int[] corpFlightBookings(int[][] bookings, int n) {
        int[] diff = new int[n];
        for (int[] tmp : bookings) {
            int l = tmp[0];
            int r = tmp[1];
            int inc = tmp[2];
            // 因为下标是从 0 开始，注意下标
            diff[l - 1] += inc;
            // 对最后一个元素的减操作可以忽略
            if (r < n) {
                diff[r] -= inc;
            }
        }
        int[] res = new int[n];
        res[0] = diff[0];
        for (int i = 1; i < n; i++) {
            res[i] = diff[i] + res[i - 1]; 
        }
        return res;
    }
    // 直白思路
    public int[] corpFlightBookings(int[][] bookings, int n) {
        // 按照给定区间 在对应的位置添加元素即可
        Map<Integer, Integer> map = new TreeMap<>();
        for (int[] booking : bookings) {
            int left = booking[0];
            int right = booking[1];
            int curNums = booking[2];
            for (int i = left; i <= right; i++) {
                map.put(i, map.getOrDefault(i, 0) + curNums);
            }
        }
        List<Integer> ans = new ArrayList<>();
        for (int i = 1; i <= n; i++) {
            ans.add(map.getOrDefault(i, 0));
        }
        return ans.stream().mapToInt(i -> i).toArray();
        // List<Integer> res = new ArrayList<>(map.values());
        // return res.stream().mapToInt(i -> i).toArray();
    }
}
```

## 2021年9月

### [165. 比较版本号](https://leetcode-cn.com/problems/compare-version-numbers/)

**题目：**

```
给你两个版本号 version1 和 version2 ，请你比较它们。

版本号由一个或多个修订号组成，各修订号由一个 '.' 连接。每个修订号由 多位数字 组成，可能包含 前导零 。每个版本号至少包含一个字符。修订号从左到右编号，下标从 0 开始，最左边的修订号下标为 0 ，下一个修订号下标为 1 ，以此类推。例如，2.5.33 和 0.1 都是有效的版本号。

比较版本号时，请按从左到右的顺序依次比较它们的修订号。比较修订号时，只需比较 忽略任何前导零后的整数值 。也就是说，修订号 1 和修订号 001 相等 。如果版本号没有指定某个下标处的修订号，则该修订号视为 0 。例如，版本 1.0 小于版本 1.1 ，因为它们下标为 0 的修订号相同，而下标为 1 的修订号分别为 0 和 1 ，0 < 1 。

返回规则如下：

如果 version1 > version2 返回 1，
如果 version1 < version2 返回 -1，
除此之外返回 0。
```

**思路：**

根据题目给定的规则，按照比较给出的字符串的大小即可。可以通过两个指针分别指向两个字符串所在的位置。然后构造出两个`.`之间的数字大小，进行比较。

时间复杂度：$O(N)$

空间复杂度：$O(1)$

**题解：**

```java
class Solution {
  // 数组方式
  public int compareVersion(String version1, String version2) {
        // 思路
        // 按照规则 进行字符串比较大小
        // 以每个. 作为分隔符 比较当前字符串的大小即可
        
        String[] fist = version1.split("\\.");
        String[] second = version2.split("\\.");
        int m = fist.length;
        int n = second.length;
        int i = 0;
        int j = 0;
        while (i < m || j < n) {
            int a = 0;
            int b = 0;
            if (i < m) {
                a = Integer.valueOf(fist[i]);
            }
            if (j < n) {
                b = Integer.valueOf(second[j]);
            }
            //System.out.println(a + " " + b);
            if (a != b) {
                return a > b ? 1 : -1;
            }
            i++;
            j++;
        }
        return 0;
    }
  
    public int compareVersion(String version1, String version2) {
        // 思路
        // 按照规则 进行字符串比较大小
        // 以每个. 作为分隔符 比较当前字符串的大小即可
        int m = version1.length();
        int n = version2.length();
        
        int i = 0;
        int j = 0;
        while (i < m || j < n) {
            int a = 0;
            int b = 0;
            while (i < m && version1.charAt(i) != '.') {
                a = a * 10 + (version1.charAt(i) - '0');
                i++;
            }
            
            while (j < n && version2.charAt(j) != '.') {
                b = b * 10 + (version2.charAt(j) - '0');
                j++;
            }
            
            if (a != b) {
                if (a < b) {
                    return -1;
                } 
                return 1;
            }
            // 过滤 . 
            i++;
            j++;
        }
        return 0;
    }
}
```

### [剑指 Offer 22. 链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

**题目：**

```
输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。

例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。
```

**思路：**

双指针遍历即可，快指针先移动k个元素，然后快慢指针共同移动，直到**快指针指向空**

Tips:

> 删除倒数第k个节点时，可以终止条件可以设置为快指针指向最后一个节点，这样慢指针刚好指向待删除节点的前驱，通过前驱节点删除，方便些

**题解：**

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        if (head == null || k < 0) {
            return head;
        }
        ListNode fast = head;
        ListNode slow = head;
        while (k-- > 0 && fast != null) {
            fast = fast.next;
        }
        while (fast != null) {
            slow = slow.next;
            fast = fast.next;
        }
        return slow;
    }
}
```

### [面试题 17.14. 最小K个数](https://leetcode-cn.com/problems/smallest-k-lcci/)

**题目：**

```java
设计一个算法，找出数组中最小的k个数。以任意顺序返回这k个数均可。

示例：
输入： arr = [1,3,5,7,2,4,6,8], k = 4
输出： [1,2,3,4]
```

**思路：**

topK的基础版本，对于topK问题，有很多种的基础解决思路：

1. 堆排序
   - 根据题意，大根堆维护前k小的元素、小根堆维护前k大的元素
2. 快排/快速选择
   - 快速选择相比于快排，在每次的选择当前的位置时，会抛弃不需要的区间，只排序需要的区间
3. 计数排序
   - 明确数据范围，可以选择计数排序，时间复杂度$O(n)$

**题解：**

```java
private int k;
    public int[] smallestK(int[] arr, int k) {
        if (arr == null || arr.length == 0) {
            return arr;
        }
        int n = arr.length;
      	return heapySort(arr, k);
        //quickSelect(arr, 0, n - 1, k - 1);
        //return Arrays.copyOf(arr, k);
      	//return countSor(arr, k);
      
    }

		public int[] heapySort(int[] arr, int k) {
        Queue<Integer> queue = new PriorityQueue<>((x, y) -> (y - x));
        for (int num : arr) {
            queue.offer(num);
            if (queue.size() > k) {
                queue.poll();
            }
        }
        return new ArrayList<>(queue).stream().mapToInt(i -> i).toArray();
    }
		
		public int[] countSort(int[] arr, int k) {
        // 计数排序
        // 思路 : 明确内部数据的范围， 说白了就是做词频统计
        int n = arr.length;
        int[] countArray = new int[10000];
        for (int num : arr) {
            countArray[num]++;
        }
        int[] ans = new int[k];
        int idx = 0;
        for (int i = 0; i < 10000; i++) {
            if (k <= 0) {
                break;
            }
            if (countArray[i] == 0) {
                continue;
            }
            int count = countArray[i];
            while (count > 0 && k > 0) {
                ans[idx++] = i;
                k--;
                count--;
            }
        }
        return ans;
    }

    private void quickSelect(int[] arr, int i, int j, int k) {
        if (i > j) {
            return;
        }
        int pos = partition(arr, i, j);
        if (pos == k) {
            return;
        } else if (pos < k) {
            quickSelect(arr, pos + 1, j, k);
        } else {
            quickSelect(arr, i, pos - 1, k);
        }
    }
    
    private int partition(int[] arr, int left, int right) {
        int cur = arr[left];
        while (left < right) {
            while (left < right && arr[right] >= cur) {
                right--;
            }
            arr[left] = arr[right];
            while (left < right && arr[left] <= cur) {
                left++;
            }
            arr[right] = arr[left];
        }
        arr[left] = cur;
        return left;
    }
}
```

### [剑指 Offer 10- I. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

**题目：**

```
写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项（即 F(N)）。斐波那契数列的定义如下：
F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1
```

**思路：**

动态规划

**题解：**

```java
class Solution {
    private int mod = (int)1e9 + 7;
    public int fib(int n) {
        if (n < 2) {
            return n;
        }
        int a = 0;
        int b = 1;
        int c = 0;
        for (int i = 2; i <= n; i++) {
            c = (a + b) % mod;
            a = b;
            b = c;
        }
        return b;
    }
}
```

### [673. 最长递增子序列的个数](https://leetcode-cn.com/problems/number-of-longest-increasing-subsequence/)

这道题题属于DP里面子序列模型的升级版，很经典。一般对于求最长上升子序列之后，题目会让我们求对应的子序列中的元素，或者求个数。

**题目：**

```
给定一个未排序的整数数组，找到最长递增子序列的个数。

示例 1:

输入: [1,3,5,4,7]
输出: 2
解释: 有两个最长递增子序列，分别是 [1, 3, 4, 7] 和[1, 3, 5, 7]。
示例 2:

输入: [2,2,2,2,2]
输出: 5
解释: 最长递增子序列的长度是1，并且存在5个子序列的长度为1，因此输出5。
注意: 给定的数组长度不超过 2000 并且结果一定是32位有符号整数。
```

**思路：**

按照最长递增子序列模型来做，开一个数组$longest$用于记录最长递增子序列的长度。同时还需要额外添加一个辅助数组$counts$，用于记录以当前元素为结尾时，最长递增子序列的个数。

1. 求$longest[i]$：

   - 根据定义，由于每个数都能独自一个成为子序列，因此起始必然有 $longest[i] = 1$

   - 枚举区间$[0, i)$中的所有数$nums[j]$寻找符合条件的$nums[i] > nums[j]$位置$j$

     > 其实也能推断出来，$longest[i]$值的增加 只能通过$longest[j] + 1$得到。

     - $longest[i] = Math.max(longest[i], longest[j] + 1)$

2. 求$counts[i]$

   - 根据定义，由于每个数都能独自一个成为子序列，因此起始必然有 $counts[i] = 1$
   - 枚举区间$[0, i)$中的所有数$nums[j]$寻找符合条件的$nums[i] > nums[j]$位置$j$（也就是在计算$longest[i]$时）
     - $longest[j] + 1 > longest[i]$时
       - $longest[i]$被$longest[j] +1$直接更新，所以$counts[i] = counts[j]$
     - $longest[j] + 1 = longest[i]$时
       - 说明找到了其直接前驱，需要进行累加：$counts[i] += counts[j]$

**举例说明：**

![demo](https://cdn.jsdelivr.net/gh/Winniekun/cloudImg@master/uPic/image-20210920112044628.png)

**题解：**

```java
class Solution {
    public int findNumberOfLIS(int[] nums) {
        int n = nums.length;
        // 用于统计 以当前元素为结尾时，最长递增子序列的长度
        int[] longest = new int[n];
        // 用于统计 以当前元素为结尾时，递增子序列的个数
        int[] counts = new int[n];
        Arrays.fill(longest, 1);
        Arrays.fill(counts, 1);
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) { // 符合递增子序列条件
                    if (longest[i] < longest[j] + 1) {
                        longest[i] = longest[j] + 1;
                        counts[i] = counts[j];
                    } else if (longest[i] == longest[j] + 1) {
                        counts[i] += counts[j]; 
                    }
                }
            }
        }
        int ans = 0;
        int maxLong = Arrays.stream(longest).max().getAsInt();
        for (int i = 0; i < n; i++) {
            if (longest[i] == maxLong) {
                ans += counts[i];
            }
        }
        return ans;
    }
}
```



## 2021年10月

### [166. 分数到小数](https://leetcode-cn.com/problems/fraction-to-recurring-decimal/)

**题目：**

```
给定两个整数，分别表示分数的分子 numerator 和分母 denominator，以 字符串形式返回小数 。

如果小数部分为循环小数，则将循环的部分括在括号内。

如果存在多个答案，只需返回 任意一个 。

对于所有给定的输入，保证 答案字符串的长度小于 104 。

示例 1：

输入：numerator = 1, denominator = 2
输出："0.5"
示例 2：

输入：numerator = 2, denominator = 1
输出："2"
示例 3：

输入：numerator = 2, denominator = 3
输出："0.(6)"
示例 4：

输入：numerator = 4, denominator = 333
输出："0.(012)"
示例 5：

输入：numerator = 1, denominator = 5
输出："0.2"
```

**思路：**

题目的核心就是模拟除法的运算。正常计算除法的过程：

1. 判断正负

2. 整除，结果为整数直接返回

3. 小数部分，对当前的余数补0然后继续求余，直到当前的余数为0。只会出现两种情况：`无限循环小数`、`有限小数`

   > PS. 到这里，从人工模拟除法运算的过程，我们就可以知道「为什么不会出现“无限不循环小数”」，因为始终是对余数进行补零操作，再往下进行运算，而余数个数具有明确的上限（有限集）。所以根据抽屉原理，一直接着往下计算，最终结果要么是「出现相同余数」，要么是「余数为 0，运算结束」。
   >
   > [宫水三叶](https://leetcode-cn.com/problems/fraction-to-recurring-decimal/solution/gong-shui-san-xie-mo-ni-shu-shi-ji-suan-kq8c4/)

所以整体看来，我们的任务就是

1. 判断结果的正负
   - $a \times b < 0$
2. 计算整数位的结果
   * $a \% b$
3. 计算小数位的结果
   - 使用map记录，当前的余数的结果，key: 当前余数，value：当前所在的位置
   - 当余数为0时 或者 map中存当前余数，终止循环

然后将上述的内容进行拼接。

> 注意 爆int，因为两个数据范围$[-2^{31}, 2^{31} - 1]$， 譬如numerator = Integer.MIN_VALUE, denominator = -1

**题解：**

```java
class Solution {
    public String fractionToDecimal(int numerator, int denominator) {
        // 防止整型溢出
        long a = numerator;
        long b = denominator;
        if (a % b == 0) {
            return String.valueOf(a / b);
        }
        StringBuilder sb = new StringBuilder();
        if (a * b < 0) {
            sb.append("-");
        } 
        a = Math.abs(a);
        b = Math.abs(b);
        sb.append(a / b);
        sb.append(".");
        a %= b;
        Map<Long, Integer> map = new HashMap<>();
        int idx = sb.length();
        while (a != 0) {
            map.put(a, idx);
            a *= 10;
            sb.append(a / b);
            a %= b;
            if (map.containsKey(a)) {
                // 找到循环区间
                int start = map.get(a);
                return String.format("%s(%s)", sb.substring(0, start), sb.substring(start));
            }
            idx++;
        }
        return sb.toString();
    }
}
```

### [482. 密钥格式化](https://leetcode-cn.com/problems/license-key-formatting/)

**题目：**

```
有一个密钥字符串 S ，只包含字母，数字以及 '-'（破折号）。其中， N 个 '-' 将字符串分成了 N+1 组。

给你一个数字 K，请你重新格式化字符串，使每个分组恰好包含 K 个字符。特别地，第一个分组包含的字符个数必须小于等于 K，但至少要包含 1 个字符。两个分组之间需要用 '-'（破折号）隔开，并且将所有的小写字母转换为大写字母。

给定非空字符串 S 和数字 K，按照上面描述的规则进行格式化。
```



**思路：**

纯模拟题目，不过为了方便处理，我们可以从后往前迭代，这样就不用额外处理第一组元素需要放入几个元素了

**题解：**

```java
class Solution {
    public String licenseKeyFormatting(String s, int k) {
        // 思路
        // 从后往前
        // 避免首部额外处理问题
        char[] arr = s.toCharArray();
        int n = arr.length;
        int idx = 0;
        StringBuilder sb = new StringBuilder();
        for (int i = n - 1; i >= 0; i--) {
            if (!Character.isLetterOrDigit(arr[i])) {
                continue;
            }
            if (idx == k) {
                sb.append("-");
                idx = 0;
            }
            sb.append(Character.toUpperCase(arr[i]));
            idx++;
        }
        return sb.reverse().toString();
    }
}
```

### [187. 重复的DNA序列](https://leetcode-cn.com/problems/repeated-dna-sequences/)

**题目：**

```
所有 DNA 都由一系列缩写为 'A'，'C'，'G' 和 'T' 的核苷酸组成，例如："ACGAATTCCG"。在研究 DNA 时，识别 DNA 中的重复序列有时会对研究非常有帮助。

编写一个函数来找出所有目标子串，目标子串的长度为 10，且在 DNA 字符串 s 中出现次数超过一次。
```



**思路：**

字符串模拟，不过要需要统计次数，两种思路：

1. 使用两个set，第一个set存储所有分割的长度为10的字符串，第二个存储重复出现的长度为10的字符串
2. 考虑 次数统计，使用map

**题解：**

```java
class Solution {
    public List<String> findRepeatedDnaSequences(String s) {
        // 思路
        // 长度为10的子串
        // 从 [0, n - 10]
        // 使用set存储已经出现的结果, 保证唯一
        int n = s.length();
        Set<String> set = new HashSet<>();
        Set<String> already = new HashSet<>();
        for (int i = 0; i <= n - 10; i++) {
            String innerStr = s.substring(i , i + 10);
            if (already.contains(innerStr)) {
                set.add(innerStr);
            }
            already.add(innerStr);
            
        }
        return set.stream().collect(Collectors.toList());
    }
}

class Solution {
    public List<String> findRepeatedDnaSequences(String s) {
        // 思路
        // 长度为10的子串
        // 从 [0, n - 10]
        // 使用set存储已经出现的结果, 保证唯一
        Map<String, Integer> map = new HashMap<>();
        int n = s.length();
        for (int i = 0; i + 10 <= n; i++) {
            String innerStr = s.substring(i, i + 10);
            map.put(innerStr, map.getOrDefault(innerStr, 0) + 1);
        }
        List<String> res = map.entrySet().stream().filter(entry -> (entry.getValue() > 1)).map(Map.Entry::getKey).collect(Collectors.toList());
        return res;
    }
}
```
