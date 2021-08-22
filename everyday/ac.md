## 2021年8月

### [3806. 最小化字符串](campus.didiglobal.com/campus_apply/didiglobal/24429#/?anchorName=default_joblist&sourceToken=&_k=3a471v)

**题目：**

```tex
给定一个包含 n 个小写字母的字符串 s。
现在，你可以将其中的最多一个字符移除（也可以不移除任何字符），你的目标是使这个字符串在字典序上尽可能小。输出你可以得到的字典序上最小的字符串。
  
输入格式:
第一行包含整数 T，表示共有 T 组测试数据。
  
每组数据第一行包含整数 n。
第二行包含 n 个小写字母构成的字符串 s。
  
输入样例：
2
3
aaa
5
abcda

输出样例：
aa
abca
```

**思路：**

因为要求字典序小，将字符转化为数字，然后按照下标位置落在二维的坐标轴上的话，我们只需要寻找第一个拐点(极大值)位置即可，然后将改位置的元素去除，后续的元素整体前移一位

时间复杂度：$O(n)$

空间复杂度：$O(n)$

**题解：**

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int T = Integer.parseInt(sc.nextLine());
        while (T-- > 0) {
            int n = Integer.parseInt(sc.nextLine());
            String a = sc.nextLine();
            cal(n, a);
        }
    }
    
    private static void cal(int n, String str) {
        // 寻找第一个拐点
        int idx = n - 1;
        StringBuilder sb = new StringBuilder();
        char[] arr = str.toCharArray();
        for (int i = 0; i < n - 1; i++) {
            if (arr[i] > arr[i + 1]) {
                idx = i;
                break;
            }
            sb.append(arr[i]);
        }
        for (int i = idx + 1; i < n; i++) {
            sb.append(arr[i]);
        }
        System.out.println(sb.toString());
    }
}
```



### 3807. 构造字符串

**题目：**

```java
给定两个整数 n 和 k，请你构造一个长度为 n 的字符串 s。

字符串 s 需满足：
s 由前 k 个小写字母构成，且前 k 个小写字母均在 s 中出现至少一次。
前 k 个小写字母中，出现次数最少的字母的出现的次数尽可能多。
输出任意满足条件的字符串 s。
```

**思路：**

**出现次数最少的字母的出现的次数尽可能多：**直白来讲，就是尽可能平均分

$k$个$k$个输出前$k$个小写字母，最后取模剩下的全输出第$k$个小写字母

**题解：**

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int T = sc.nextInt();
        while (T-- > 0) {
            int n = sc.nextInt();
            int k = sc.nextInt();
            cal(n, k);
        }
    }
    
    private static void cal(int n, int k) {
        StringBuilder sb = new StringBuilder();
        int time = n / k;
        int rst = n % k;
        for (int i = 0; i < k - 1; i++) {
            for (int j = 0; j < time; j++) {
                sb.append((char)('a' + i));
            }
        }
        
        for (int i = 0; i < time + rst; i++) {
            sb.append((char)('a' + k - 1));
        }
        System.out.println(sb.toString());
    }
}
```

### [3808. 画正方形](https://www.acwing.com/problem/content/3811/)

今天的题目有些长，就不粘题目了:wink:

**思路：** 数学

我们先来枚举$n$较小的情况，设需要尺子的边数为$ans$

$n = 1, ans = 2$

$n = 2, ans = 3$

---

$n = 3-4, ans = 4$

$n = 5 - 6, ans = 5$

---

$n = 7 - 9, ans = 6$

$n = 10 - 12, ans = 7$

---

$n = 13 - 16, ans = 8$

得出规律：$n = [k \times (k - 1) + 1, k \times (k + 1)], ans = 2 \times k - (2 \times k + 1)$

**题解：**

```java
import java.util.*;
public class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int T = sc.nextInt();
        while(T -- > 0){
            int n = sc.nextInt();
            int sqrt = (int) Math.sqrt(n);
            int sheng = n - sqrt * sqrt;
            int res = 2 * sqrt + (sheng > sqrt ? 2 : 1);
            if(sheng == 0)
                res -- ;
            System.out.println(res);
        }
    }
}
```

### 3809. 修改数组

**题目：**

```
给定一个长度为 n 的正整数数组 a1,a2,…,an。

你可以任意改变其中任意元素的值。

但是，改变后的元素的值仍需是正整数。

将一个元素的值从 a 变为 b 所需要付出的代价为 |a−b|。

对于一个正整数 t，如果 |ai−t|≤1，则称第 i 个元素能够与 t 匹配。

现在，请你指定一个正整数 t，并且用最小的代价修改整个数组，使得数组中所有元素都能够与 t 匹配。

指定的 t 不同，所需付出的最小代价也可能不同。

请你合理选择正整数 t，目标是让所需付出的最小代价尽可能小。

输入格式
第一行包含整数 T，表示共有 T 组测试数据。

每组数据第一行包含整数 n。

第二行包含 n 个整数 a1,a2,…,an。

输出格式
每组数据输出一行结果，首先输出你选择的正整数 t，然后输出使得数组中所有元素都能够与 t 匹配，所需付出的最小代价。

如果 t 有多种合理选择，则任选其一输出即可。

数据范围
1≤T≤20,
1≤n≤1000,
1≤ai≤100,
同一测试点内所有 n 的和不超过 1000。
```

**思路：**

1. 根据题意，看到数据范围并不大，所以直接暴力破解也不会超时

   - 时间复杂度：$O(100 \times n)$

   - 空间复杂度：$O(1)$

2. 最开始的想的是中位数的思路，但是有case过去不去，很难受，不知道什么情况。估计是数学推导有问题。。。

**题解：**

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int T = sc.nextInt();
        while (T-- > 0) {
            int n = sc.nextInt();
            int[] arr = new int[n];
            for (int i = 0; i < n; i++) {
                arr[i] = sc.nextInt();
            }
            cal(arr, n);
        } 
    }
    
    private static void cal(int[] arr, int n) {
        Arrays.sort(arr);
        int sum = Integer.MAX_VALUE;
        int ans = 0;
        // 大力出奇迹
        for (int i = 0; i <= 100; i++) {
            int innerSum = genSum(arr, i);
            if (innerSum < sum) {
                ans = i;
                sum = innerSum;
            }
        }
        System.out.println(ans + " " + sum);
    }
    
    private static int genSum(int[] arr, int target) {
        int left = target - 1;
        int right = target + 1;
        int sum = 0;
        for (int num : arr) {
            if (num < target) {
                sum += left - num;
            } else if (num > target) {
                sum += num - right;
            }
        }
        return sum;
    }
}
```

### 3810. 最长连续休息时间

**题目：**

```tex
一天可以被分为 n 个时段。

一个工人的每日工作安排可以用一个长度为 n 的 01 序列 a1,a2,…,an 来表示。

ai 为 0 表示第 i 个时间段是工作时间，ai 为 1 表示第 i 个时间段是休息时间。

工人日复一日的严格按照这个工作安排来进行工作和休息。

请问，工人的最长连续休息时间有多长（单位：时段）？

注意，连续休息时间可能跨天。

保证工人至少在一个时间段处于工作状态。

输入格式
第一行包含整数 T，表示共有 T 组测试数据。

每组数据第一行包含整数 n。

第二行包含 n 个整数 a1,a2,…,an。

输出格式
每组数据输出一行结果，表示最长连续休息时间。

数据范围
1≤T≤10,
1≤n≤2×105,
0≤ai≤1,
同一测试点内所有 n 的和不超过 2×105。
```

**思路：**

最开始想的是双指针，$i$用于遍历当前所在位置

1. 如果$arr[i] = 1$ 开始统计当前有多少连续的1
2. 如果$arr[i] = 0$ continue

时间复杂度：$O(n ^2)$ 会TLE

空间复杂度：$O(1)$

**优化：**

因为上述的思路会重复计算很多，因为要求连续，所以直接使用一个$count$统计当前连续1的个数即可，如果遇到当前元素不为$0$，则直接$count = 0$即可。类似求字符串A是否为字符串B的一个子串的思路。[551. 学生出勤记录 I](https://leetcode-cn.com/problems/student-attendance-record-i/)也有体现

**题解：**

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int T = sc.nextInt();
        while (T-- > 0) {
            int n = sc.nextInt();
            int[] arr = new int[n];
            for (int i = 0; i < n; i++) {
                arr[i] = sc.nextInt();
            }
            cal(arr, n);
        }
    }
    
    // 连续的子数组
    private static void cal(int[] arr, int n) {
        int[] ans = new int[2 * n];
        int max = 0;
        System.arraycopy(arr, 0, ans, 0, n);
        System.arraycopy(arr, 0, ans, n, n);
        int len = 2 * n;
        int count = 0;
        for (int i = 0; i < len; i++) {
            if (ans[i] == 1) {
                count++;
            } else {
                max = Math.max(max, count);
                count = 0;
            }
        }
        System.out.println(max);
    }
}
```

### [3814. 矩阵变换](https://www.acwing.com/problem/content/3817/)

**题目：**

```
给定一个 n×n 的 01 矩阵。

你可以选择若干列（也可以不选），并将这些列上的所有元素进行变换（1 变 0，0 变 1）。

你的目标是使得矩阵中有尽可能多的行满足：一行中的所有元素都为 1。

输出可以得到的满足条件的行的最大数量。

输入格式
第一行包含整数 n。

接下来 n 行，每行包含一个长度为 n 的 01 字符串，表示整个矩阵。

输出格式
输出可以得到的满足条件的行的最大数量。

数据范围
1≤n≤100
```

**思路：**

最开始理解错了，看到数据量不大，直接模拟就行了。时间复杂度：$O(n^2)$，空间复杂度$O(n ^ 2)$，不过后面注意到是随机选取若干列。那么按照排列组合来看，时间复杂度$O(n!)$，突然感觉思路不对了。后面根据题意，自己草稿纸上算了算了个，emmm，这不就是字符串的词频统计。。。输出频率最高的即可:face_with_head_bandage:，做题还是先仔细的过过脑子，不要想当然！！！

**题解：**

```java
import java.util.*;
public class Main {
    static Map<String, Integer> map = new HashMap<>();
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = Integer.parseInt(sc.nextLine());
        int res = 1;
        String line;
        for (int i = 0; i < n; i++) {
            line = sc.nextLine();
            map.put(line, map.getOrDefault(line,0) +1);
            res = Math.max(res, map.get(line));
        }
        System.out.println(res);
    }
}
```

