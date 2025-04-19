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