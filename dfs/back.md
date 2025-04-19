回溯算法是对树形或者图形结构执行一次深度优先遍历，实际上类似枚举的搜索尝试过程，在遍历的过程中寻找问题的解。

深度优先遍历有个特点：当发现已不满足求解条件时，就返回，尝试别的路径。此时对象类型变量就需要重置成为和之前一样，称为「状态重置」。

许多复杂的，规模较大的问题都可以使用回溯法，有「通用解题方法」的美称。实际上，回溯算法就是暴力搜索算法，它是早期的人工智能里使用的算法，借助计算机强大的计算能力帮助我们找到问题的解。



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



### [842. 将数组拆分成斐波那契序列](https://leetcode.cn/problems/split-array-into-fibonacci-sequence/)

> 这个其实是求字符串所有子串的变形，变形内容如下：
>
> - 求子串的过程中，判断是否是斐波那契数
>
> ```java
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

