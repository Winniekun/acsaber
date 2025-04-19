链表作为基础的数据结构，将链表的操作掌握了，其他的相关结构与算法，理论上来说问题不大了。LeetCode中， 链表的操作是有迹可循的， 当然也可当场理解，然后画图，找解法（奈何我太笨，编码功底还不够），反正链表的所有操作都离不开基础的`CRUD`, 还需要注意的是，当涉及到头结点的操作的时候，最好设置一个`dummy`节点指向头结点，这样方便统一处理。

## 基础

#### 增

需要注意构造新链表时的`头插法` 和`尾插法`

`头插法`： 逆序

`尾插法`： 正序

#### 删

**根据前驱删除**

```java
pre.next = pre.next.next;
```

**根据当前节点删除**

```java
ListNode next = cur.next;
cur.val = next.val;
cur.next = next.next;
```

#### 改

无

#### 查

```java
// 查询长度
while (cur != null) {
  cur = cur.next
}

// 查最后一个
while (cur.next != null) {
  cur = cur.next
}
```



## 删除

### [83. 删除排序链表中的重复元素](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/)

> 给定一个已排序的链表的头 `head` ， *删除所有重复的元素，使每个元素只出现一次* 。返回 *已排序的链表* 。

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode dummy = new ListNode(-1, head);
        ListNode p = head;
        while (p != null) {
            while (p.next != null && p.val == p.next.val) {
                p.next = p.next.next;
            }
            p = p.next;
            
        }
        return dummy.next;
    }
}
```

### [82. 删除排序链表中的重复元素 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/)

> 给定一个已排序的链表的头 `head` ， *删除原始链表中所有重复数字的节点，只留下不同的数字* 。返回 *已排序的链表* 。

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode dummy = new ListNode(-1, head);
        ListNode pre = dummy;
        ListNode cur = head;
        while (cur != null) {
            while (cur.next != null && cur.next.val == cur.val) {
                cur = cur.next;
            }
            if (pre.next != cur) {
                pre.next = cur.next;
                cur = cur.next;
            } else {
                pre = pre.next;
                cur = cur.next;
            }
        }   
        return dummy.next;
    }
}
```





## 链表反转

### [206. 反转链表](https://leetcode.cn/problems/reverse-linked-list/)

> 给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode dummy = new ListNode(-1);
        while (head != null) {
            ListNode next = head.next;
            head.next = dummy.next;
            dummy.next = head;
            head = next;
        }   
        return dummy.next;
    }
}
```

### [92. 反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/)

> 给你单链表的头指针 `head` 和两个整数 `left` 和 `right` ，其中 `left <= right` 。请你反转从位置 `left` 到位置 `right` 的链表节点，返回 **反转后的链表** 。

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        // 找到两个位置，然后使用头插法插入到尾部
        ListNode dummy = new ListNode(-1, head);
        // 第一个前驱
        ListNode firstPre = findNode(dummy, left - 1);   
        // 第二个
        ListNode second = findNode(dummy, right);
        while (firstPre.next != second) {
            ListNode cur = firstPre.next;
            firstPre.next = cur.next;
            cur.next = second.next;
            second.next = cur;
        }
        return dummy.next;
    }

    private ListNode findNode(ListNode head, int idx) {
        while (idx-- > 0) {
            head = head.next;
        }
        return head;
    }
}
```



## 链表合并

### [21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)

> 将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode dummy = new ListNode(-1);
        // 尾插法 保持顺序
        ListNode tail = dummy;
        while (list1 != null && list2 != null) {
            if (list1.val >= list2.val) {
                tail.next = list2;
                list2 = list2.next;
            } else {
                tail.next = list1;
                list1 = list1.next;
            }
            tail = tail.next;
        }
        if (list1 != null) {
            tail.next = list1;
        }
        if (list2 != null) {
            tail.next = list2;
        }
        return dummy.next;
    }
}
```



### [23. 合并 K 个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)

> 给你一个链表数组，每个链表都已经按升序排列。
>
> 请你将所有链表合并到一个升序链表中，返回合并后的链表。

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        // 参考合并两个有序链表
        ListNode ans = null;
        for (ListNode node : lists) {
            ans = merget(ans, node);
        }
        return ans;
    }

    private ListNode merget(ListNode first, ListNode second) {
        ListNode dummy = new ListNode(-1);
        ListNode tail = dummy;
        while (first != null && second != null) {
            if (first.val > second.val) {
                tail.next = second;
                second = second.next;
            } else {
                tail.next = first;
                first = first.next;
            }
            tail = tail.next;
        }

        if (first != null) {
            tail.next = first;
        }
        if (second != null) {
            tail.next = second;
        }
        return dummy.next;
    }
}
```





## 快慢指针

### [876. 链表的中间结点](https://leetcode.cn/problems/middle-of-the-linked-list/)

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }
}
```

### [143. 重排链表](https://leetcode.cn/problems/reorder-list/)

> 找到中间节点，然后合并两个节点，注意需要断开中间节点，不然会构造成环
>
> 譬如：1->2->3->4
>
> - 找到中间节点 3，然后逆序 3 - 4部分的链表，结果为 4 -> 3
> - 但是之前的节点 1 -> 2 -> 3 还存在
> - 1和2中链表节点3是同一个节点，3.next = 3 构造成环了

```java
class Solution {
    public void reorderList(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        ListNode pre = null;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            pre = slow;
            slow = slow.next;
        }
        if (pre == null) {
            return;
        }
        pre.next = null;
        ListNode newHead = reverse(slow);
        // 链表合并
        ListNode dummy = new ListNode(-1);
        ListNode tail = dummy;
        int i = 0;
        while (head != null && newHead != null) {
            if (i % 2 == 0) {
                tail.next = head;
                head = head.next;
            } else {
                tail.next = newHead;
                newHead = newHead.next;
            }
            tail = tail.next;
            i++;
        }
        while (newHead != null) {
            tail.next = newHead;
            newHead = newHead.next;
            tail = tail.next;
        }
        head = dummy.next;
    }

    private ListNode reverse(ListNode head) {
        ListNode dummy = new ListNode(-1);
        while (head != null) {
            ListNode next = head.next;
            head.next = dummy.next;
            dummy.next = head;
            head = next;
        }
        return dummy.next;
    }
}
```



### [234. 回文链表](https://leetcode.cn/problems/palindrome-linked-list/)

> 快慢指针用来快速找到中间节点，如果最后 fast != null， 说明链表长度为奇数 n % 2 != 0 

```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }        
        // 奇数长度链表 slow后移一位
        if (fast != null) {
            slow = slow.next;
        }
        ListNode newHead = reverse(slow);
        while (head != null && newHead != null) {
            if (head.val != newHead.val) {
                return false;
            }
            head = head.next;
            newHead = newHead.next;
        }
        return true;
 
    }

    private ListNode reverse(ListNode node) {
        ListNode dummy = new ListNode(-1);
        while (node != null) {
            ListNode next = node.next;
            node.next = dummy.next;
            dummy.next = node;
            node = next;
        }
        return dummy.next;
    }
}
```



### [141. 环形链表](https://leetcode.cn/problems/linked-list-cycle/)

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode dummy = new ListNode(-1, head);
        ListNode fast = dummy;
        ListNode slow = dummy;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {
                return true;
            }
        }
        return false;
    }
}
```



### [142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        ListNode tmp = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            // 相遇
            if (slow == fast) {
                while (tmp != slow) {
                    tmp = tmp.next;
                    slow = slow.next;
                }
                return tmp;
            }
        }
        return null;
    }
}
```



### [19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

> 双指针

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // 双指针
        ListNode dummy = new ListNode(-1, head);
        ListNode fast = dummy;
        ListNode slow = dummy;
        while (n-- >= 0) {
            fast = fast.next;
        }
        while (fast != null) {
            fast = fast.next;
            slow = slow.next;
        }
        if (slow.next != null) {
            slow.next = slow.next.next;
        }
        return dummy.next;
    }
}
```

