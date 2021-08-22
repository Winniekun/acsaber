## 第12周周赛

进场时间晚了些，气啊，第二题题目看了好久才看明白，然后草稿纸上归纳了下，开始编码，期间还出现了一些小细节。。。

最后在结束之后的1分钟内提交了。。。

![12周周赛](https://cdn.jsdelivr.net/gh/Winniekun/cloudImg@master/uPic/image-20210814202833670.png)

### AcWing 3803. 数组去重

按照题意来做即可，然后因为是按照从后往前的顺序，所以考虑使用栈来辅助

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
        Deque<Integer> stack = new LinkedList<>();
        Set<Integer> set = new HashSet<>();
        for (int i = n - 1; i >= 0; i--) {
            if (set.contains(arr[i])) {
                continue;
            }
            stack.push(arr[i]);
            set.add(arr[i]);
        }
        printArr(stack);
    }
    
    private static void printArr(Deque<Integer> stack) {
        System.out.println(stack.size());
        while (!stack.isEmpty()) {
            System.out.print(stack.pop() + " ");
        }
        System.out.println();
    }
}
```

### AcWing 3804. 构造字符串

题目读起来脑阔疼，最后理解之后，整理如下

先将元素集合排序的字符数组$arr$​，之后共分为三种情况： $\left\{ \begin{array}{rcl} k >n   && {前n位不变，后[n + 1, k]位补最小元素}\\ k < n &&{从第k位开始遍历，找到第一个不是最大元素val的位置idx，切换为arr中比val大1的元素， [idx + 1, k]位置补最小元素} \\ k = n &&{从第n位开始遍历，找到第一个不是最大元素val的位置idx，切换，[idx + 1, k]位置补最小元素}\end{array} \right. $​

因为考虑到需要快速获取元素的位置，以及根据位置获取元素，所以使用了两个map处理映射关系

最后的实现如下，实现的不是很好，可读性不高

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int T = Integer.parseInt(sc.nextLine());
        while (T-- > 0) {
            String[] line = sc.nextLine().split(" ");
            int n = Integer.parseInt(line[0]);
            int k = Integer.parseInt(line[1]);
            String str = sc.nextLine();
            cal(str, n, k);
        }
    }

    private static void cal(String str, int n, int k) {
        // 贪心
        char[] arr = str.toCharArray();
        Set<Character> set = new HashSet<>();
        for (char ch : arr) {
            set.add(ch);
        }
        List<Character> list = new ArrayList<>(set);
        Collections.sort(list);
        char[] standard = new char[list.size()];
        int idx = 0;
        for (Character character : list) {
            standard[idx++] = character;
        }
        Map<Character, Integer> map = new HashMap<>();
        Map<Integer, Character> idxMap = new HashMap<>();
        int m = standard.length;
        for (int i = 0; i < m; i++) {
            map.put(standard[i], i);
            idxMap.put(i, standard[i]);
        }
        StringBuilder sb = new StringBuilder();
        if (k > n) {
            for (char c : arr) {
                sb.append(c);
            }
            int rst = k - n;
            while (rst > 0) {
                sb.append(standard[0]);
                rst--;
            }
            System.out.println(sb.toString());
        } else if (k < n) {
            int ik = k;
            while (k > 0 && map.get(arr[k - 1]) == m - 1) {
                k--;
            }
            for (int i = 0; i < k - 1; i++) {
                sb.append(arr[i]);
            }
            char cur = arr[k - 1];
            int innerIdx = 0;
            for (int i = 0; i < m; i++) {
                if (standard[i] == cur) {
                    innerIdx = i;
                    break;
                }
            }
            sb.append(standard[innerIdx + 1]);
            int curN = sb.length();
            while (curN < ik) {
                sb.append(standard[0]);
                curN++;
            }
            System.out.println(sb.toString());
        } else {
            int next = 0;
            int curIdx = 0;
            for (int i = n - 1; i >= 0; i--) {
                int cur = map.get(arr[i]);
                if (cur == m - 1) {
                    continue;
                }
                next  = cur + 1;
                curIdx = i;
                break;
            }
            for (int i = 0; i <= curIdx - 1; i++) {
                sb.append(arr[i]);
            }
            sb.append(idxMap.get(next));
            for (int i = curIdx + 1; i < n; i++) {
                sb.append(standard[0]);
            }
            System.out.println(sb.toString());
        }
    }
}
```



## 第15场周赛 

![15周周赛](https://cdn.jsdelivr.net/gh/Winniekun/cloudImg@master/uPic/image-20210904225530248.png)

### [3826. 青蛙跳 ](https://www.acwing.com/problem/content/3829/)

**题解：**

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int T = sc.nextInt();
        while (T-- > 0) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            int k = sc.nextInt();
            int left, right;
            if (k % 2 == 1) {
                left = k / 2 + 1;
            } else {
                left = k / 2;
            }
            right = k / 2;
            long lJump = (long)left * a;
            long rJump = (long)right * b;
            System.out.println(lJump - rJump);
        }
    }
}
```

### [3827. 最小正整数](https://www.acwing.com/problem/content/3830/)

**题目：**

```
给定两个整数 n 和 k。

请你计算，末尾至少有连续 k 个 0，并且可以被 n 整除的最小正整数。

例如，当 n=375,k=4 时，满足条件的最小正整数为 30000。

输入格式
第一行包含整数 T，表示共有 T 组测试数据。

每组数据占一行，包含两个整数 n,k。

输出格式
每组数据输出一行结果，表示满足条件的最小正整数。

数据范围
所有数据满足 1≤T≤10，1≤n≤109，0≤k≤8。
```

**思路：**

题目很好理解，细节是魔鬼。主要思路就是统计$n$中质因子$2$的个数$count_2$，$5$的个数$count_5$。然后根据题目给定的最少的$0$的个数$k$，分类讨论（$min(count_2, count_5) = 当前已存在0的数量$）

1. $count_2 < count_5$

   1. $k >= count_5$
      - 需要额外添加$10^{k - count_5}$
   2. $k < count_5$
      - $k > 0$ 添加 空缺的2使得 $count_2 = count_5$

2. $count_5 < count_2$

   同理

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
        if (k == 0) {
            System.out.println(n);
            return;
        }
        int tmp = n;
        int count = 0;
        while (tmp % 10 == 0) {
            count++;
            tmp /= 10;
        }
        if (count >= k) {
            System.out.println(n);
            return;
        }
        int count2 = 0;
        int count5 = 0;
        int base = n;
        while (n % 2 == 0) {
            n /=2;
            count2++;
        }
        while (n % 5 == 0) {
            n /= 5;
            count5++;
        }
        // count < k
        count2 -= count;
        count5 -= count;
        k -= count;
        if (count2 != 0) {
            int need5 = 0;
            int need0 = 0;
            if (k >= count2) {
                need5 = count2;
                need0 = k - count2;
            } else {
                need5 = k > 0 ? k : 1;
            }
            long res = (long)(Math.pow(10, need0) * Math.pow(5, need5) * base);
            System.out.println(res);
        } else {
            int need2 = 0;
            int need0 = 0;
            if (k > count5) {
                need2 = count5;
                need0 = k - count5;
            } else {
                need2 = k > 0 ? k : 1;
            }
            long res = (long)(Math.pow(10, need0) * Math.pow(2, need2) * base);
            System.out.println(res);
        }
    }
}
```

## 第17场周赛

这周太浪了，本来想着上床睡觉了，然后突然返现周六了。。。瞬间从床上跳下来，开始搞AcWing的周赛。。。。19：30多入场。。。血亏

![17周周赛](https://cdn.jsdelivr.net/gh/Winniekun/cloudImg@master/uPic/image-20210918203148349.png)

### [3971. 最小的商](https://www.acwing.com/problem/content/3974/)

**题目：**

```
给定一个长度为 n 的整数数组 a1,a2,…,an 以及一个整数 k。

请你找到一个数组元素 ai，要求：

k 是 ai 的倍数。
k 除以 ai 的商尽可能小。
输出这个最小的商。

输入格式
第一行包含整数 T，表示共有 T 组测试数据。

每组数据第一行包含两个整数 n,k。

第二行包含 n 个整数 a1,a2,…,an。

输出格式
每组数据输出一行结果，一个整数，表示最小的商。

保证每组数据一定有解。
```

**思路：**

按照题意来

**题解：**

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int T = sc.nextInt();
        while (T-- > 0) {
            int max = Integer.MAX_VALUE;
            int n = sc.nextInt();
            int k = sc.nextInt();
            for (int i = 0; i < n; i++) {
                int num = sc.nextInt();
                if (k % num == 0) {
                    max = Math.min(max, k / num);
                }
            }
            System.out.println(max);
        } 
    }
}
```

### [3972. 方格集数量](https://www.acwing.com/problem/content/3975/)

**题目：**

```
给定一个 n×m 的方格矩阵。

每个方格要么是黑色，要么是白色。

请你计算，共有多少个非空方格集合满足：

集合内的所有方格颜色都相同。
集合内的任意两个方格都在同一行或同一列上。
输入格式
第一行包含两个整数 n,m。

接下来 n 行，每行包含 m 个整数，每个整数要么是 0，要么是 1，用来表示矩阵中每个方格的颜色，0 表示颜色为白，1 表示颜色为黑。

输出格式
一个整数，表示满足条件的非空方格集合数量。
```

**思路：**

说实话，题目没有理解，看了10多分钟，然后想了下就是直接统计行、列中相同的0、1的个数，接着全排列。不过答案不对，最后想了下，应该是2的幂次，解决

**题解：**

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int rows = sc.nextInt();
        int cols = sc.nextInt();
        int[][] matrix = new int[rows][cols];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                matrix[i][j] = sc.nextInt();
            }
        }
        cal(matrix, rows, cols);
    } 
    
    private static void cal(int[][] matrix, int rows, int cols) {
        long sum = 0;
        for (int i = 0; i < rows; i++) {
            sum += countRows(matrix, i, cols, 0);
            sum += countRows(matrix, i, cols, 1);
        }
        
        for (int i = 0; i < cols; i++) {
            sum += countCols(matrix, rows, i, 0);
            sum += countCols(matrix, rows, i, 1);
        }
        System.out.println(sum - rows * cols);
    }
    
    private static long countRows(int[][] matrix, int row, int cols, int target) {
        long count = 0;
        for (int i = 0; i < cols; i++) {
            if (matrix[row][i] == target) {
                count++;
            }
        }
        return (long)(Math.pow(2, count) - 1);
    }
    
    private static long countCols(int[][] matrix, int rows, int col, int target) {
        long count = 0;
        for (int i = 0; i < rows; i++) {
            if (matrix[i][col] == target) {
                count++;
            }
        }
        return (long)(Math.pow(2, count) - 1);
    }
}
```

