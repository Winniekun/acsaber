回溯算法是对树形或者图形结构执行一次深度优先遍历，实际上类似枚举的搜索尝试过程，在遍历的过程中寻找问题的解。

深度优先遍历有个特点：当发现已不满足求解条件时，就返回，尝试别的路径。此时对象类型变量就需要重置成为和之前一样，称为「状态重置」。

许多复杂的，规模较大的问题都可以使用回溯法，有「通用解题方法」的美称。实际上，回溯算法就是暴力搜索算法，它是早期的人工智能里使用的算法，借助计算机强大的计算能力帮助我们找到问题的解。



## [46. 全排列](https://leetcode.cn/problems/permutations/)

> 排列和组合不一样，排列只要位置不同计算一种结果

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        int n = nums.length;
        boolean[] visited = new boolean[n];
        List<Integer> path = new ArrayList<>();
        List<List<Integer>> res = new ArrayList<>();
        backtrack(nums, visited, path, res);
        return res;
    }

    private void backtrack(int[] nums, boolean[] visited, List<Integer> path, List<List<Integer>> res) {
        if (path.size() == nums.length) {
            res.add(new ArrayList<>(path));
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            if (visited[i]) {
                continue;
            }
            path.add(nums[i]);
            visited[i] = true;
            backtrack(nums, visited, path, res);
            visited[i] = false;
            path.remove(path.size() - 1);
        }
    }
}
```



## [22. 括号生成](https://leetcode.cn/problems/generate-parentheses/)

> 先生成左边，再生成右边

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        String path = "";
        backtrack(0, 0, n, path, res);
        return res;
    }

    private void backtrack(int left, int right, int max, String path, List<String> res) {
      // 不执行撤销操作是因为当前层path固定，使用String，如果是StringBuilder因为可变，需要撤销
        if (path.length() == 2 * max) {
            res.add(path);
            return;
        }
        if (left < max) {
            backtrack(left + 1, right, max, path + "(", res);
        }
        if (right < left) {
            backtrack(left, right + 1, max, path + ")", res);
        }
    }
}

// stringbuilder 类型
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        StringBuilder path = new StringBuilder();
        backtrack(0, 0, n, path, res);
        return res;
    }

    private void backtrack(int left, int right, int max, StringBuilder path, List<String> res) {
        // 不执行撤销操作是因为当前层path固定，使用String，如果是StringBuilder因为可变，需要撤销
        if (path.length() == 2 * max) {
            res.add(new String(path));
            return;
        }
        if (left < max) {
            backtrack(left + 1, right, max, path.append("("), res);
            path = path.deleteCharAt(path.length() - 1);

        }
        if (right < left) {
            backtrack(left, right + 1, max, path.append(")"), res);
            path = path.deleteCharAt(path.length() - 1);
        }
    }
}
```







## [17. 电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

**题解：**

```
给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。答案可以按 任意顺序 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。
示例 1：

输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
示例 2：

输入：digits = ""
输出：[]
示例 3：

输入：digits = "2"
输出：["a","b","c"]
```

**思路：**

组合问题，只不过把多个值通过数字做了映射，所以是一个map反向解析接口。

- 每次遍历到第i个位置，获取当前数组的字符串，然后继续回溯

**题解：**

```java
class Solution {
    public List<String> letterCombinations(String digits) {
        List<String> res = new ArrayList<>();
        if (digits.length() == 0 || digits == "") {
            return res;
        }
        Map<String, String[]> map = new HashMap<>();
        map.put("2", new String[]{"a", "b", "c"});
        map.put("3", new String[]{"d", "e", "f"});
        map.put("4", new String[]{"g", "h", "i"});
        map.put("5", new String[]{"j", "k", "l"});
        map.put("6", new String[]{"m", "n", "o"});
        map.put("7", new String[]{"p", "q", "r", "s"});
        map.put("8", new String[]{"t", "u", "v"});
        map.put("9", new String[]{"w", "x", "y", "z"});
        dfs(digits, digits.length(), 0, new StringBuilder(), res, map);
        return res;
    }

    private void dfs(String digits, int n, int starter, StringBuilder sb, List<String> res, Map<String, String[]> map) {
        if (sb.length() == n) {
            res.add(new String(sb));
            return;
        }
        String str = digits.substring(starter, starter + 1);
        String[] arr = map.get(str);
        for (String row : arr) {
            sb.append(row);
            dfs(digits, n, starter + 1, sb, res, map);
            sb.deleteCharAt(sb.length() - 1);
        }
    }
}

```



## [842. 将数组拆分成斐波那契序列](https://leetcode.cn/problems/split-array-into-fibonacci-sequence/)

> 这个其实是求字符串所有子串的变形，变形内容如下：
>
> - 求子串的过程中，判断是否是斐波那契数
>
> ```java
> // 所有子串
> // 直接字符串截断
> private void backtrack(String num, int idx, List<String> tmp, List<List<String>> res) {
>     if (idx == num.length()) {
>         res.add(new ArrayList<>(tmp));
>         return;
>     }
>     for (int i = idx; i < num.length(); i++) {
>         String cur = num.substring(idx, i + 1);
>         tmp.add(cur);
>         backtrack(num, i + 1, tmp, res);
>         tmp.remove(tmp.size() - 1);
>     }
> }
> 
> // 通过数字计算拼凑
> private void backtrackToNumber(String num, int idx, List<Integer> tmp, List<List<Integer>> res) {
>     if (idx == num.length()) {
>         res.add(new ArrayList<>(tmp));
>         return;
>     }
>     int number = 0;
>     for (int i = idx; i < num.length(); i++) {
>         number = number * 10 + num.charAt(i) - '0';
>         tmp.add(number);
>         backtrackToNumber(num, i + 1, tmp, res);
>         tmp.remove(tmp.size() - 1);
>     }
> }
> ```
>
> 

```java
class Solution {
    public List<Integer> splitIntoFibonacci(String S) {
        List<Integer> res = new ArrayList<>();
        backtrack(S, 0, res);
        return res;
    }

    private boolean backtrack(String s, int index, List<Integer> path) {
        if (index == s.length()) {
            return path.size() >= 3;
        }

        long num = 0;
        for (int i = index; i < s.length(); i++) {
            // 前导0情况
            if (i > index && s.charAt(index) == '0') break;

            num = num * 10 + (s.charAt(i) - '0');
            if (num > Integer.MAX_VALUE) break;

            int size = path.size();
            if (size >= 2) {
                long sum = (long) path.get(size - 1) + path.get(size - 2);
                if (num < sum) continue;
                else if (num > sum) break;
            }

            path.add((int) num);
            if (backtrack(s, i + 1, path)) return true;
            path.remove(path.size() - 1); // 回溯
        }

        return false;
    }
}
```



## [93. 复原 IP 地址](https://leetcode.cn/problems/restore-ip-addresses/)

> 回溯模板改造，需要判断，和LC842逻辑类似

```java
class Solution {
    public List<String> restoreIpAddresses(String s) {
        List<String> res = new ArrayList<>();
        String path = "";
        backtrack(s, 0, 0, path, res);
        return res;
    }

    private void backtrack(String s, int idx, int segment, String path, List<String> res) {
        // 添加元素
        if (segment == 4 && s.length() == idx) {
            res.add(path.substring(0, path.length() - 1));
            return;
        }
        if (segment > 4 || idx >= s.length()) {
            return;
        }
        // 处理当前层
        // 元素长度最多为3
        for (int i = 1; i <=3; i++) {
            if (idx + i  > s.length()) {
                break;
            }
            String cur = s.substring(idx, idx + i);
            // 对当前元素校验
            if (cur.length() > 1 && cur.startsWith("0")) {
                break;
            }
            if (cur.length() >= 1&& Integer.parseInt(cur) >  255) {
                break;
            }
            backtrack(s, idx + i, segment + 1, path + cur + ".", res);
        }
        
    }
}
```



### [79. 单词搜索](https://leetcode.cn/problems/word-search/)

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        int row = board.length;
        int col = board[0].length;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                boolean[][] visited = new boolean[row][col];
                if (find(board, visited, i, j, word, 0)) {
                    return true;
                }                
            }
        }
        return false;
    }

    private boolean find(char[][] board, boolean[][] visited, int i, int j, String word, int idx) {
        if (idx == word.length()) {
            return true;
        }
        if (!isPos(i, j, board.length, board[0].length) || visited[i][j] || word.charAt(idx) != board[i][j]) {
            return false;
        }
        visited[i][j] = true;
        int[][] steps = new int[][] {{-1, 0}, {1, 0}, {0, 1}, {0, -1}};
        boolean res = false;
        for (int[] step : steps) {
            int nI = i + step[0];
            int nJ = j + step[1];
           // 因为当前层已经判断了位置是否合法，所以不判断，避免误判导致数据丢失
           // board[][] = [["a"]],  word = "a", 这里如果加了位置合法判断就永远无法进入下一层，就不会触发 true的条件 
            boolean innerRes = find(board, visited, nI, nJ, word, idx + 1);
            res |= innerRes;
        }
        visited[i][j] = false;
        return res;

    }

    private boolean isPos(int i, int j, int col, int row) {
        return (i >= 0 && i < col) && (j >= 0 && j < row);
    }
}
```

