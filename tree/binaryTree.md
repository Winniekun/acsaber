## 基础模板

### 前序遍历



### 中序遍历



### 后续遍历



### 层次遍历



## 题目汇总

### 前序遍历相关



### 中序遍历相关

> 大多数与二叉搜索数相关

#### [105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

**题目：**

```
给定两个整数数组 preorder 和 inorder ，其中 preorder 是二叉树的先序遍历， inorder 是同一棵树的中序遍历，请构造二叉树并返回其根节点。
```

**思路：**

**题解：**

```java
class Solution {

    private int[] preorder;

    private int[] inorder;

    private int starter = 0;

    private Map<Integer, Integer> map;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        this.preorder = preorder;
        this.inorder = inorder;
        map = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return buildTree(0, inorder.length - 1);
    }

    private TreeNode buildTree(int left, int right) {
        if (left > right) {
            return null;
        }
        int cur = preorder[starter++];
        int idx = map.get(cur);
        TreeNode root = new TreeNode(cur);
        root.left = buildTree(left, idx - 1);
        root.right = buildTree(idx + 1, right);
        return root;
    }
}
```



#### [108. 将有序数组转换为二叉搜索树](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)

> 和通过前序遍历+中序遍历构造二叉树类似，只不过依赖了二叉搜索树的特性，做了特殊处理



#### [98. 验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

**题目：**

```
给你一个二叉树的根节点 root ，判断其是否是一个有效的二叉搜索树。

有效 二叉搜索树定义如下：

节点的左子树只包含 小于 当前节点的数。
节点的右子树只包含 大于 当前节点的数。
所有左子树和右子树自身必须也是二叉搜索树。
```

**思路：**

根据二叉搜索树中序遍历结果为递增数列，判断中序遍历的结果是否为递增数列就行。

**题解：**

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode p = root;
        TreeNode pre = null;
        while (p != null || !stack.isEmpty()) {
            while (p != null) {
                stack.push(p);
                p = p.left;
            }
            if (!stack.isEmpty()) {
                p = stack.pop();
                if (pre != null && pre.val >= p.val) {
                    return false;
                }
                pre = p;
                p = p.right;
            }
        }
        return true;
    }
}
```



#### [230. 二叉搜索树中第 K 小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-bst/)

**题目：**

给定一个二叉搜索树的根节点 `root` ，和一个整数 `k` ，请你设计一个算法查找其中第 `k` 小的元素（从 1 开始计数）。

**思路：**

采用二叉搜索树中序遍历特性

**题解：**

```java
class Solution {
    public int kthSmallest(TreeNode root, int k) {
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode p = root;
        stack.push(p);
        while (p != null || !stack.isEmpty()) {
            while (p != null) {
                stack.push(p);
                p = p.left;
            }

            if (!stack.isEmpty()) {
                p = stack.pop();
                if (--k == 0) {
                    return p.val;
                }
                p = p.right;
            }
        }
        return -1;
    }
}
```

### 后续遍历相关

#### [114. 二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)

**题目：**

**思路：**

要变遍历变转化，如果使用前序遍历，直接就破坏了整棵树的结构，中序遍历同理，所以需要从最末尾开始构造，所以采用后续遍历，同时，要将已经构造的链表迁移到当前节点的`right`前面，所以需要需要重新遍历下已经构造好的链表到最后位置，然后将`root.right`链上即可

**题解：**

```java
class Solution {
    public void flatten(TreeNode root) {
        if (root == null) {
            return;
        }
        flatten(root.left);
        flatten(root.right);
        TreeNode tmp = root.right;
        root.right = root.left;
        root.left = null;
        TreeNode cur = root;
        while (cur.right != null) {
            cur = cur.right;
        }
        cur.right = tmp;
    }
}
```



#### [124. 二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

**题目：**

**思路：**

涉及到最值的问题，都可以优先考虑后续遍历。

定义递归函数，返回的是当前节点的最大路径值

- 当前节点的路径最大值：Math.max(left, right) + roo.val , 如果结果<0的话，直接返回0。
- 整体最大值：Math.max(res,  (left + right + val))

**题解：**

```java
class Solution {
    private int max = Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        postOrder(root);
        return max;
    }
    
    private int postOrder(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left = postOrder(root.left);
        int right = postOrder(root.right);
        int innerValue = left + right + root.val;
        max = Math.max(max, innerValue);
        int outerMax = root.val + Math.max(left, right);
        return outerMax < 0 ? 0 : outerMax;

    }
}
```



#### [236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

**题目：**

```
给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”
```



**思路：**

 后续遍历，定义函数用于找到最近的公共祖先，对于最外层的递归调用者来说，返回值是最近公共祖先的意思。但是，在递归过程中，递归函数的返回值有多个含义，可以是空节点、节点 p、节点 q 或者最近公共祖先。代码仅仅根据「左右子树返回的节点是否为空」来确定返回值，至于左右子树返回的具体是哪个节点，我们并不在乎。

问：为什么发现当前节点是 p 或者 q 就不再往下递归了？万一下面有 q 或者 p 呢？

答：如果下面有 q 或者 p，那么当前节点就是最近公共祖先，直接返回当前节点。如果下面没有 q 和 p，那既然都没有要找的节点了，也不需要递归，直接返回当前节点。

**题解：**

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return null;
        }
        if (p.val == root.val || q.val == root.val) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left != null && right != null) {
            return root;
        }
        return left != null ? left : right;
    }
}
```



### 层次遍历相关

#### [102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)



**题解：**

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



#### [199. 二叉树的右视图](https://leetcode.cn/problems/binary-tree-right-side-view/)

**题目：**

```
给定一个二叉树的 根节点 root，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。
```

**思路：**

层次遍历，每层最后一个数据放入结果集中。

**题解：**

```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        Queue<TreeNode> queue = new ArrayDeque<>();
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        TreeNode p = root;
        queue.offer(p);
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                p = queue.poll();
                if (i == size - 1) {
                    res.add(p.val);
                }
                if (p.left != null) {
                    queue.offer(p.left);
                }
                if (p.right != null) {
                    queue.offer(p.right);
                }
            }
        }
        return res;
    }
}
```

