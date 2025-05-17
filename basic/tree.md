## 序

对于二叉树而言，做到最后，最大的感觉就是对树`前`、`中`、`后`、`层次`遍历的花式操作。感觉后面难度较高的就是后序遍历和动归的结合---`树形DP`。对于`树形DP`而言（后序遍历的深入理解），明确当前层能够获取的结果以及向上传递什么结果即可。

## 基础遍历

### [144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/)

> 非递归

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode p = root;
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        while (!stack.isEmpty() || p != null) {
            while (p != null) {
                stack.push(p);
                res.add(p.val);
                p = p.left;
            }
            if (!stack.isEmpty()) {
                p = stack.pop();
                p = p.right;
            }
        }
        return res;
    }
}
```

> 递归



### [94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

> 非递归

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode p = root;
        List<Integer> res = new ArrayList<>();
        while (p != null || !stack.isEmpty()) {
            while (p != null) {
                stack.push(p);
                p = p.left;
            }
            
            if (!stack.isEmpty()) {
                p = stack.pop();
                res.add(p.val);
                p = p.right;
            }
        }
        return res;
    }
}
```



### [145. 二叉树的后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/)

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode p = root;
        TreeNode prev = null; // 记录上一次访问的节点
        List<Integer> res = new ArrayList<>();
        
        while (p != null || !stack.isEmpty()) {
            while (p != null) {
                stack.push(p);
                p = p.left;
            }

            TreeNode curr = stack.peek();
            // 如果右子树为空，或者右子树已经访问过了，则访问当前节点
            if (curr.right == null || curr.right == prev) {
                res.add(curr.val);
                stack.pop();
                prev = curr;
                p = null; // 不往右走
            } else {
                p = curr.right; // 继续往右子树走
            }
        }
        return res;
    }
}
```



### [102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        TreeNode p = root;
        queue.offer(p);
        while (!queue.isEmpty()) {
            int size = queue.size();
            List<Integer> cur = new ArrayList<>();
            for (int i = 0; i < size; i++) {
                p = queue.poll();
                cur.add(p.val);
                if (p.left != null) {
                    queue.offer(p.left);
                }
                if (p.right != null) {
                    queue.offer(p.right);
                }
            }
            res.add(cur);
        }
        return res;
    }
}
```



## 回溯

### [257. 二叉树的所有路径](https://leetcode.cn/problems/binary-tree-paths/)

> 遍历后需要复原，有两种方式
>
> - 递归入参直接new StringBuilder()，这样当前函数的path信息不会被直接修改
> - 递归完，重新复原path

```java
// new版本
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        StringBuilder sb = new StringBuilder();
        dfs(root, sb, res);
        return res;
    }

    private void dfs(TreeNode root, StringBuilder path, List<String> res) {
        if (root == null) {
            return;
        }
        path.append(root.val);
        if (root.left == null && root.right == null) {
            res.add(new String(path));
        } else {
            path.append("->");
            dfs(root.left, new StringBuilder(path), res);
            dfs(root.right, new StringBuilder(path), res);
        }
    }
}

// 复原版本
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        StringBuilder sb = new StringBuilder();
        dfs(root, sb, res);
        return res;
    }

    private void dfs(TreeNode root, StringBuilder path, List<String> res) {
        if (root == null) {
            return;
        }
        int n = path.length();
        path.append(root.val);
        if (root.left == null && root.right == null) {
            res.add(new String(path));
        } else {
            path.append("->");
            dfs(root.left, path, res);
            dfs(root.right, path, res);
        }
        path.setLength(n);
    }
}

```



### [LCR 153. 二叉树中和为目标值的路径](https://leetcode.cn/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

> 按照前序遍历的思路，再引入回溯的思想
>
> - 注意：
>   - 如果curNumer = target了，需要做的是将当前的结果添加到集合中，但是不能直接return，这样容易导致回溯逻辑被破坏

```java
class Solution {
    private List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> pathTarget(TreeNode root, int target) {
        preOrder(root, target, 0, new ArrayList<>());
        return res;
    }

    private void preOrder(TreeNode root, int target, int curSum, List<Integer> path) {
        if (root == null) {
            return;
        }
        curSum += root.val;
        path.add(root.val);
        if (target == curSum && root.left == null && root.right == null) {
            res.add(new ArrayList<>(path));
        }
        preOrder(root.left, target, curSum, path);
        preOrder(root.right, target, curSum, path);
        path.remove(path.size() - 1);
        curSum -= root.val;
    }
}
```



