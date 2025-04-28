## 滑动窗口

> 高端模拟
>
> 最开始移动的指针r，如果没有明确的指定跳跃步长，可以使用for循环方便些



## 题目

### [76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

```java
class Solution {
    public String minWindow(String S, String t) {
        char[] s = S.toCharArray();
        int m = s.length;
        int ansLeft = -1;
        int ansRight = m;
        int[] cntS = new int[128]; // s 子串字母的出现次数
        int[] cntT = new int[128]; // t 中字母的出现次数
        for (char c : t.toCharArray()) {
            cntT[c]++;
        }

        int left = 0;
        for (int right = 0; right < m; right++) { // 移动子串右端点
            cntS[s[right]]++; // 右端点字母移入子串
            while (isCovered(cntS, cntT)) { // 涵盖
                if (right - left < ansRight - ansLeft) { // 找到更短的子串
                    ansLeft = left; // 记录此时的左右端点
                    ansRight = right;
                }
                cntS[s[left]]--; // 左端点字母移出子串
                left++;
            }
        }
        return ansLeft < 0 ? "" : S.substring(ansLeft, ansRight + 1);
    }

    private boolean isCovered(int[] cntS, int[] cntT) {
        for (int i = 'A'; i <= 'Z'; i++) {
            if (cntS[i] < cntT[i]) {
                return false;
            }
        }
        for (int i = 'a'; i <= 'z'; i++) {
            if (cntS[i] < cntT[i]) {
                return false;
            }
        }
        return true;
    }
}
```



### [567. 字符串的排列](https://leetcode.cn/problems/permutation-in-string/)

```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        // 窗口值完全相同
        int n = s2.length();
        int m = s1.length();
        int[] cnt1 = new int[26];
        int[] cnt2 = new int[26];
        for (int i = 0; i < m; i++) {
            cnt1[s1.charAt(i) - 'a']++;
        }
        int l = 0;
        for (int r = 0; r < n; r++) {
            cnt2[s2.charAt(r) - 'a']++;
            if (r - l + 1 < m) {
                continue;
            }
            if (Arrays.equals(cnt1, cnt2)) {
                return true;
            }
            cnt2[s2.charAt(l) - 'a']--;
            l++;
        }
        return false;
    }
}
```



### [438. 找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        // 定长滑动窗口
        int n = p.length();
        int m = s.length();
        int[] cntP = new int[26];
        int[] cntS = new int[26];
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            cntP[p.charAt(i) - 'a']++;
        }
        for (int r = 0; r < m; r++) {
            cntS[s.charAt(r) - 'a']++;
            int l = r - n + 1;
            // 长度未达到窗口值
            if (l < 0) {
                continue;
            }
            // 出现次数相同
            if (Arrays.equals(cntP, cntS)) {
                res.add(l);
            }
            cntS[s.charAt(l) - 'a']--;
        }
        return res;
    }
}
```

### [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        // 滑动窗口 添加窗口自身特性 去重
        Queue<Character> queue = new ArrayDeque<>();
        int n = s.length();
        int max = 0;
        for (int i = 0; i < n; i++) {
            while (!queue.isEmpty() && queue.contains(s.charAt(i))) {
                queue.poll();
            }
            queue.offer(s.charAt(i));
            max = Math.max(max, queue.size());

        }
        return max;
    }
}
```

