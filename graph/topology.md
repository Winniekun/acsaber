## [207. 课程表](https://leetcode.cn/problems/course-schedule/)

**题目：**

```
你这个学期必须选修 numCourses 门课程，记为 0 到 numCourses - 1 。

在选修某些课程之前需要一些先修课程。 先修课程按数组 prerequisites 给出，其中 prerequisites[i] = [ai, bi] ，表示如果要学习课程 ai 则 必须 先学习课程  bi 。

例如，先修课程对 [0, 1] 表示：想要学习课程 0 ，你需要先完成课程 1 。
请你判断是否可能完成所有课程的学习？如果可以，返回 true ；否则，返回 false 。

 

示例 1：

输入：numCourses = 2, prerequisites = [[1,0]]
输出：true
解释：总共有 2 门课程。学习课程 1 之前，你需要完成课程 0 。这是可能的。
示例 2：

输入：numCourses = 2, prerequisites = [[1,0],[0,1]]
输出：false
解释：总共有 2 门课程。学习课程 1 之前，你需要先完成​课程 0 ；并且学习课程 0 之前，你还应先完成课程 1 。这是不可能的。

```

**思路：**

- 拓扑排序

**题解：**

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        // 拓扑排序，判断图是否有环
        // 图使用领接表存储
        List<List<Integer>> adj = new ArrayList<>();
        int[] indegree = new int[numCourses];
        for (int i = 0; i < numCourses; i++) {
            adj.add(new ArrayList<>());
        }
        for (int[] prerequisite : prerequisites) {
            int pre = prerequisite[1];
            int cur = prerequisite[0];
            indegree[cur]++;
            // 记录节点对应的下一个元素
            adj.get(pre).add(cur);
        }
        Queue<Integer> queue = new ArrayDeque<>();
        // 寻找入度为0的位置
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) {
                queue.offer(i);
            }
        }
        int count = 0;
        while (!queue.isEmpty()) {
            int cur = queue.poll();
            count++;
            List<Integer> next = adj.get(cur);
            for (int num : next) {
                if (--indegree[num] == 0) {
                    queue.offer(num);
                }     
            }
        }
        return count == numCourses;

    }
}
```

