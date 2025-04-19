

### [150. 逆波兰表达式求值](https://leetcode.cn/problems/evaluate-reverse-polish-notation/)

> 无括号 优先考虑+ - 后考虑 * /

```java
class Solution {
    public int evalRPN(String[] tokens) {
        // 栈
        Deque<Integer> stack = new ArrayDeque<>();
        for (String str : tokens) {
            // 无括号，所以优先考虑+ - 后考虑 * /
            if ("+".equals(str)) {
                int first = stack.pop();
                int second = stack.pop();
                stack.push(first + second);
            } else if ("-".equals(str)) {
                int first = stack.pop();
                int second = stack.pop();
                stack.push(second - first);
            } else if ("*".equals(str)) {
                int first = stack.pop();
                int second = stack.pop();
                stack.push(first * second);
            } else if ("/".equals(str)) {
                int first = stack.pop();
                int second = stack.pop();
                stack.push(second / first);
            } else {
                stack.push(Integer.parseInt(str));
            }
        }
        return stack.pop();
    }
}
```



### [20. 有效的括号](https://leetcode.cn/problems/valid-parentheses/)

```java
class Solution {
    public boolean isValid(String s) {
        Map<Character, Character> map = new HashMap<>();
        map.put(')', '(');
        map.put(']', '[');
        map.put('}', '{');
        Deque<Character> stack = new ArrayDeque<>();
        char[] arr = s.toCharArray();
        for (char ch : arr) {
            if (!map.containsKey(ch)) {
                stack.push(ch);
            } else {
                // 右边符号
                if (!stack.isEmpty() && stack.peek() == map.get(ch)) {
                    stack.pop();
                } else {
                    return false;
                }
            }
        }
        return stack.isEmpty();
    }
}
```



### [84. 柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

> 多理解

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        int[] arr = new int[n + 2];
        for (int i = 1; i < n + 1; i++) {
            arr[i] = heights[i - 1];
        }
        Deque<Integer> stack = new ArrayDeque<>();
        int ans = 0;
        for (int i = 0; i < n + 2; i++) {
            while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) {
                int height = arr[stack.pop()];
                int left = stack.peek();
                int len = i - left - 1;
                int area = len * height;
                ans = Math.max(ans, area);
            }
            stack.push(i);
        }

        return ans;
    }
}
```



### [42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/)

> 理解其实不难

```java
class Solution {
    public int trap(int[] height) {
        // 遍历整个数组，借用单调递减栈记录历史数据
        Deque<Integer> stack = new ArrayDeque<>();
        int ans = 0;
        int n = height.length;
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && height[stack.peek()] < height[i]) {
                // 找到高度低于当前高度的索引
                int idx = stack.pop();
                // 弹出所有高度相同的元素
                while (!stack.isEmpty() && height[stack.peek()] == height[idx]) {
                    stack.pop();
                }
                // 如果栈不为空，说明能够计算实际的面积
                if (!stack.isEmpty()) {
                    // area = (Math.min(left, right) - height[idx]) * (rightIdx - leftIdx - 1)
                    int leftIdx = stack.peek();
                    int left = height[leftIdx];
                    int right = height[i];
                    int area = (Math.min(left, right) - height[idx]) * (i - leftIdx - 1);
                    ans += area;
                }
            }
            stack.push(i);
        }
        return ans;
    }
}
```



### [232. 用栈实现队列](https://leetcode.cn/problems/implement-queue-using-stacks/)

> 多理解

```java
class MyQueue {

    private Deque<Integer> stack1;

    private Deque<Integer> stack2;

    public MyQueue() {
        this.stack1 = new ArrayDeque<>();
        this.stack2 = new ArrayDeque<>();
    }
    
    public void push(int x) {
        stack1.push(x);   
    }
    
    public int pop() {
        if (!stack2.isEmpty()) {
            return stack2.pop();
        }
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }
        return stack2.pop();
    }
    
    public int peek() {
        if (!stack2.isEmpty()) {
            return stack2.peek();
        }
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }
        return stack2.peek();
    }
    
    public boolean empty() {
        return stack1.isEmpty() && stack2.isEmpty();
    }
}
```

