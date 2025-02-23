## 第 257 场周赛

### [统计特殊四元组](https://leetcode-cn.com/problems/count-special-quadruplets/)

**思路：**

直接暴力解决

**题解：**

```java
class Solution {
    public int countQuadruplets(int[] nums) {
        int count = 0;
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                for (int k = j + 1; k < n; k++) {
                    int sum = nums[i] + nums[j] + nums[k];
                    int x = k + 1;
                    while (x < n) {
                        if (nums[x] == sum) {
                            count++;
                        }
                        x++;
                    }
                }
            }
        }
        return count;
    }
}
```

### [游戏中弱角色的数量](https://leetcode-cn.com/problems/the-number-of-weak-characters-in-the-game/)

**思路：**

对于多维数组排序问题，按照不同的维度进行排序，问题就能将高维的问题转化为低维度的问题（降维处理，PCA？）

对于本题来说，可以参考`俄罗斯信封`，先按照 第一维度从小到大进行排序，然后第一维度相同时，按照第二维度从大到小进行排序

排序之后，我们只需要根据第二维度的数据$arr$，根据当前位置$i$寻找$j < i$中，所有小于$i$的数量。因为数据量的问题，暴力求解肯定超时，所以需要一个$O(n)$时间复杂度的方式：**单调栈**。维护一个单减的栈就行了。

**题解：**

```java
class Solution {
    public int numberOfWeakCharacters(int[][] a) {
        Arrays.sort(a, (x, y) -> (x[0] == y[0] ? y[1] - x[1] : x[0] - y[0]));
        int count = 0;
        int right = -1;
        int n = a.length;
        Deque<Integer> stack  = new ArrayDeque<>();
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && a[stack.peek()][1] < a[i][1]) {
                count++;
                stack.pop();
            }

            stack.push(i);
            // if (a[i][1] < right) {
            //     count++;
            // }
            // right = Math.max(right, a[i][1]);

        }
        return count;
    }
}
```



## 第438场周赛

> 工作后第一正式打比赛，被锤的头疼，状态得慢慢找了

### [100579. 判断操作后字符串中的数字是否相等 I](https://leetcode.cn/problems/check-if-digits-are-equal-in-string-after-operations-i/)

**思路：**

第一题暴力遍历就行了，注意过程中不要使用数字转字符串，不然导致结果为0的值直接被忽略了。

**题解：**

```java
class Solution {
   public static boolean hasSameDigits(String s) {
        while (s.length() > 2) {
            int n = s.length();
            char[] charArray = s.toCharArray();
            int j = 0;
            int total = 0;
            StringBuilder sb = new StringBuilder();
            while (j < n - 1) {
                char first = charArray[j];
                char second = charArray[j + 1];
                int val = ((first - '0') + (second - '0')) % 10;
                sb.append(val);
                j++;
            }
            s = sb.toString();
        }
        if (s.length() == 2) {
            return s.charAt(0) == s.charAt(1) ? true : false;
        } else {
            return s.equals("0") ? true : false;
        }
    }
}
```





### [100576. 提取至多 K 个元素的最大总和](https://leetcode.cn/problems/maximum-sum-with-at-most-k-elements/)

**思路：**

知道是贪心，但是最开始按照矩阵位置移动方向思考了（纯模拟），先对矩阵每行排序，最开始选择最大的元素，然后在其位置的上下左右斜对角8个方向找最值，然后当前位置置0，`limits[i] -=1`继续寻找，但是忽略一个细节

- 如果存在大量重复的元素，由于元素选择存在不可控性，可能会导致漏选

后面看了题解，直接贪心+排序就行了，方便取值可以使用优先队列进行维护取的值。

**题解：**

```java
class Solution {
    public long maxSum(int[][] grid, int[] limits, int k) {
        // 贪心，limits[i] 拿完，然后取前k个就行, 使用大根堆
        Queue<Integer> priorityQueue = new PriorityQueue<>((a, b) -> b - a);
        for (int i = 0; i < grid.length; i++) {
            int[] cur = grid[i];
            Arrays.sort(cur);
            for (int j = cur.length - limits[i]; j < cur.length; j++) {
                priorityQueue.offer(cur[j]);
            }
        }
        long res = 0;
        while (k > 0) {
            if (!priorityQueue.isEmpty()) {
                res += priorityQueue.poll();
            }
            k--;
            
        }
        return res;
    }
}
```

