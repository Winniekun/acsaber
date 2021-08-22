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

