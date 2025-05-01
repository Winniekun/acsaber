排序

> 对固定的题意进行排序，然后按照内容进行处理



## 题目

### [56. 合并区间](https://leetcode.cn/problems/merge-intervals/)

> interval[0]排序，然后比较interval[1]进行合并

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        int n = intervals.length;
        int[][] tmp = new int[n][2];
        Arrays.sort(intervals, (x, y) -> x[0] == y[0] ? x[1] - y[1] : x[0] - y[0]);
        int maxRight = 0;
        int count = 0;
        for (int[] interval : intervals) {
            int left = interval[0];
            int right = interval[1];
            // left > maxRight
            if (left > maxRight) {
                tmp[count][0] = left;
                tmp[count][1] = right;
                maxRight = right;
                count++;
            } else if (left <= maxRight && count > 0) {
                maxRight = Math.max(right, maxRight);
                tmp[count - 1][1] = maxRight;
            } else {
                tmp[count][0] = left;
                tmp[count][1] = right;
                maxRight = right;
                count++;
            }
        }
        int[][] res = new int[count][2];
        for (int i = 0; i < count; i++) {
            res[i][0] = tmp[i][0];
            res[i][1] = tmp[i][1];
        } 
        return res;
    }
}
```



### [会议室II](https://leetcode.cn/problems/meeting-rooms-ii/description/)

> 给你一个会议时间安排的数组 `intervals` ，每个会议时间都会包括开始和结束的时间 `intervals[i] = [starti, endi]` ，返回 *所需会议室的最小数量* 。

```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        // 贪心策略
        // 按照左端点排序
        // 实时更新右端点
        // 有重叠的数量+1
        // 右端点：Math.max(right, cur[1])
        int right = Integer.MIN_VALUE;
        Arrays.sort(intervals, (x, y)-> (x[0] - y[0]));
        int count = 0;
        // 存放已经使用的会议室的的结束时间， 小根堆
        PriorityQueue<Integer> queue = new PriorityQueue<>();
        for (int[] interval : intervals) {
            int innerR = interval[1];
            int innerL = interval[0];
            // 更新内部的结束时间
            if (!queue.isEmpty() && queue.peek() <= innerL) {
                queue.poll();
                queue.offer(innerR);
            }else if (queue.isEmpty() || innerL < queue.peek()) {
                queue.offer(innerR);
            }
        }
        return queue.size();
    }
}
```



### [LCR 164. 破解闯关密码](https://leetcode.cn/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

> 排序规则：按照两个数字组合出的新数字进行排序，因为刚和String的默认排序字典序相同，所以使用String的能力

```java
class Solution {
    public String crackPassword(int[] password) {
        int n = password.length;
        String[] tmp = new String[n];
        for (int i = 0; i < n; i++) {
            tmp[i] = String.valueOf(password[i]);
        }
        Arrays.sort(tmp, (x, y) -> (x + y).compareTo(y + x));
        StringBuilder sb = new StringBuilder();
        for (String str : tmp) {
            sb.append(str);
        }
        return sb.toString();
    }
}
```



### [315. 计算右侧小于当前元素的个数](https://leetcode.cn/problems/count-of-smaller-numbers-after-self/)

> 使用归并排序的性质，类似其逆序对

```java
class Solution {
    private int[] count;
    private int[] indexes;
    private int[] tempIndexes;

    public List<Integer> countSmaller(int[] nums) {
        int n = nums.length;
        count = new int[n];
        indexes = new int[n];
        tempIndexes = new int[n];

        for (int i = 0; i < n; i++) indexes[i] = i;

        mergeSort(nums, 0, n - 1);

        List<Integer> res = new ArrayList<>();
        for (int c : count) res.add(c);
        return res;
    }

    private void mergeSort(int[] nums, int left, int right) {
        if (left >= right) return;
        int mid = (left + right) / 2;
        mergeSort(nums, left, mid);
        mergeSort(nums, mid + 1, right);
        merge(nums, left, mid, right);
    }

    private void merge(int[] nums, int left, int mid, int right) {
        for (int i = left; i <= right; i++) {
            tempIndexes[i] = indexes[i];
        }

        int i = left;      // pointer for left subarray
        int j = mid + 1;   // pointer for right subarray
        int k = left;      // pointer for indexes[]

        while (i <= mid && j <= right) {
            if (nums[tempIndexes[i]] <= nums[tempIndexes[j]]) {
                // 当前左边元素小于等于右边，更新该元素被插入了多少个右侧小元素
                count[tempIndexes[i]] += (j - mid - 1);
                indexes[k++] = tempIndexes[i++];
            } else {
                // 右边元素更小，先插入它
                indexes[k++] = tempIndexes[j++];
            }
        }

        while (i <= mid) {
            count[tempIndexes[i]] += (j - mid - 1);
            indexes[k++] = tempIndexes[i++];
        }

        while (j <= right) {
            indexes[k++] = tempIndexes[j++];
        }
    }
}
```

