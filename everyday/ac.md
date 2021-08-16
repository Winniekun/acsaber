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

