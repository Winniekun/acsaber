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

