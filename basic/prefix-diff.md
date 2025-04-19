## 前缀



## 差分





### [797. 差分](https://www.acwing.com/problem/content/799/)

**题目：**

```
输入一个长度为 n 的整数序列。

接下来输入 m 个操作，每个操作包含三个整数 l,r,c，表示将序列中 [l,r] 之间的每个数加上 c。

请你输出进行完所有操作后的序列。

输入格式
第一行包含两个整数 n 和 m。

第二行包含 n 个整数，表示整数序列。

接下来 m 行，每行包含三个整数 l，r，c，表示一个操作。

输出格式
共一行，包含 n 个整数，表示最终序列。

数据范围
1≤n,m≤100000,
1≤l≤r≤n,
−1000≤c≤1000,
−1000≤整数序列中元素的值≤1000
```

**思路：**

正常的差分模板

**题解：**

```java
import java.util.*;
public class Main{
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int count = sc.nextInt();
        int[] array = new int[n];
        for (int i = 0; i < n; i++) {
            array[i] = sc.nextInt();
        }
        int[] diff = new int[n];
        diff[0] = array[0];
        for (int i = 1; i < n; i++) {
            diff[i] = array[i] - array[i - 1];
        }
        // print(diff);
        // 思路 构造前缀和，对前缀和的数组进行操作，之后根据前缀和求出素有的结果
        while (count-- > 0) {
            int l = sc.nextInt() - 1;
            int r = sc.nextInt() - 1;
            int inc = sc.nextInt();
            diff[l] += inc;
            if (r + 1 < n) {
                diff[r +1] -= inc;
            }
        }
        array[0] = diff[0];
        for (int i = 1; i < n; i++) {
            array[i] = diff[i] + array[i - 1];
        }
        for (int num : array) {
            System.out.print(num + " ");
        }
    }
    
    private static void print(int[] arr) {
        for (int num : arr) {
            System.out.print(num + " ");
        }
    }
}
```

