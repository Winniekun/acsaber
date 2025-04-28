## 二分模板

其中「二分」模板其实有两套，主要是根据 check(mid) 函数为 true 时，需要调整的是 l 指针还是 r 指针来判断。

当` check(mid) == true` 调整的是` l `时：计算` mid` 的方式应该为` mid = l + r + 1 >> 1`：

```java
long l = 0, r = 1000009;
while (l < r) {
    long mid = l + r + 1 >> 1;
    if (check(mid)) {
        l = mid;
    } else {
        r = mid - 1;
    }
}
```

当` check(mid) == true` 调整的是` r`时：计算` mid` 的方式应该为` mid = l + r >> 1`：

```java
long l = 0, r = 1000009;
while (l < r) {
  long mid = l + r >> 1;
  if (check(mid)) {
    r = mid;
  } else {
    l = mid + 1;
  }
}
```

关于「二分」模板的说明

- 为啥修改左边指针 l 的时候要进行 +1 操作？
  「模板一」的 +1 操作主要是为了避免发生「死循环」，因为 >> 和 直接使用 / 一样，都属于「下取整」操作。考虑 l = 0, r = 1 的简单情况，如果不 +1 的话，l + r >> 1 等于 0 + 1 / 2，l 仍然是 0，陷入死循环。



作者：宫水三叶
链接：https://leetcode.cn/problems/divide-two-integers/solutions/575593/shua-chuan-lc-er-fen-bei-zeng-cheng-fa-j-m73b/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



## 题目汇总

### [33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)



### [35. 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

> 1：找大于等于数的第一个位置 （满足某个条件的第一个数）
> 2：找小于等于数的最后一个数 （满足某个条件的最后一个数）
> 3.查找最大值 （满足该边界的右边界）、
> 4.查找最小值 (满足该边界的左边界) 
>
> **区间的左端点还是还是右端点**

> 这里是查找左端点的值

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int n = nums.length;
        int l = 0;
        int r = n;
        while (l < r) {
            int mid = l + r >> 1;
            if (nums[mid] >= target) {
                r = mid;
            } else {
                l = mid + 1;
            }
        }
        return l;
    }
}

```

### [153. 寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)

> 寻找第一个位置（左端点）
>
> 排除区间：nums[mid] < nums[n - 1]时，拐点位置必然在l - mid之间

```java
class Solution {
    public int findMin(int[] nums) {
        int n = nums.length;
        int l = 0, r = n - 1;
        while (l < r) {
            int mid = l + r + 1 >> 1;
            if (nums[mid] >= nums[0]) {
                l = mid;
            } else {
                r = mid - 1;
            }
        }
        return r + 1 < n ? nums[r + 1] : nums[0];
    }
}
```

### [154. 寻找旋转排序数组中的最小值 II](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/)

> 元素中有重复

```java
class Solution {
    public int findMin(int[] nums) {
        int n = nums.length;
        int l = 0, r = n - 1;
        while (l < r && nums[0] == nums[r]) r--;
        while (l < r) {
            int mid = l + r + 1 >> 1;
            if (nums[mid] >= nums[0]) {
                l = mid;
            } else {
                r = mid - 1;
            }
        }
        return r + 1 < n ? nums[r + 1] : nums[0];
    }
}
```





### [74. 搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/)

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        // 从左下开始搜索
        int row = matrix.length;
        int col = matrix[0].length;
        int i = row - 1;
        int j = 0;
        while (i >= 0 && j < col) {
            if (matrix[i][j] < target) {
                j++;
            } else if (matrix[i][j] > target) {
                i--;
            } else {
                return true;
            }
        }
        return false;
    }
}Å
```



### [1755. 最接近目标值的子序列和](https://leetcode.cn/problems/closest-subsequence-sum/)

> 核心是求组合的新方式
>
> 组合规则：从一个集合中，随机选出几个元素（不重复）

```java
import java.util.*;

public class Solution {
    public int minAbsDifference(int[] nums, int goal) {
        int n = nums.length;
        int[] left = Arrays.copyOfRange(nums, 0, n / 2);
        int[] right = Arrays.copyOfRange(nums, n / 2, n);

        List<Integer> leftSums = getAllSums(left);
        List<Integer> rightSums = getAllSums(right);
        Collections.sort(rightSums);

        int res = Integer.MAX_VALUE;
        for (int l : leftSums) {
            int remain = goal - l;
            int idx = lowerBound(rightSums, remain);

            // 尝试 idx 和 idx - 1（最接近 remain）
            if (idx < rightSums.size()) {
                res = Math.min(res, Math.abs(l + rightSums.get(idx) - goal));
            }
            if (idx > 0) {
                res = Math.min(res, Math.abs(l + rightSums.get(idx - 1) - goal));
            }
        }
        return res;
    }

    private List<Integer> getAllSums(int[] nums) {
        List<Integer> res = new ArrayList<>();
        int n = nums.length;
        for (int i = 0; i < (1 << n); i++) {
            int sum = 0;
            for (int j = 0; j < n; j++) {
                if (((i >> j) & 1) == 1) {
                    sum += nums[j];
                }
            }
            res.add(sum);
        }
        return res;
    }

    // 实现 lower_bound
    private int lowerBound(List<Integer> list, int target) {
        int low = 0, high = list.size();
        while (low < high) {
            int mid = (low + high) / 2;
            if (list.get(mid) >= target) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }
        return low;
    }
}

```

