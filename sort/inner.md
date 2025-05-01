> 内部排序整理

## 插入排序

> 从 i - n 中选择元素，插入到0-i中已经排序号的数组中

```java
private void inserSort(int[] nums) {
    int n = nums.length;
    for (int i = 1; i < n; i++) {
        int j = i - 1;
        int cur = nums[i];
        while (j >= 0 && nums[j] >= cur) {
            nums[j + 1] = nums[j];
            j--;
        }
        nums[j + 1] = cur;
    }
}
```



## 选择排序

> 开启一个循环，每轮从未排序区间选择最小的元素，将其放到已排序区间的末尾。

```java
private void selectSort(int[] nums) {
    for (int i = 0; i < n; i++) {
        int minIdx = i;
        int j = i;
        while (j < n) {
            minIdx = nums[j] < nums[minIdx] ? j : minIdx;
            j++;
        }
        int tmp = nums[minIdx];
        nums[minIdx] = nums[i];
        nums[i] =  tmp;
    }
} 
```



## 快速排序

```java
// 快速排序
private void quickSort(int[] nums, int left, int right) {
        if (left > right) {
            return;
        }
        int pivoit = partition(nums, left, right);
        quickSort(nums, left, pivoit - 1);
        quickSort(nums, pivoit + 1, right);
    }
    
private int partition(int[] nums, int left, int right) {
    // 看选择的值从那边开始，如果是左边，则从右边开始遍历，如果右边，则从左边开始遍历 避免第一个值被覆盖
    int pos = nums[left];
    while (left < right) {
        while (left < right && nums[right] >= pos) {
            right--;
        }
        nums[left] = nums[right];
        while (left < right && nums[left] <= pos) {
            left++;
        }
        nums[right] = nums[left];
    }
    nums[left] = pos;
    return left;
}
```





## 归并排序

```java
private void mergeSort(int[] nums, int left, int right) {
  			// 因为是 l + r >> 1
        // 譬如遇到 0, 1情况，mid = 0了，会死循环
        if (left >= right) {
            return;
        }
        int mid = left + right >> 1;
        mergeSort(nums, left, mid);
        mergeSort(nums, mid + 1, right);
        merge(nums, left, mid, right);
    }

private void merge(int[] nums, int left, int mid, int right) {
        if (left > right) {
            return;
        }
        int n = right - left + 1;
        int[] tmp = new int[n];
        int k = 0;
        int l = left;
        int r = mid + 1;
        while (l <= mid && r <= right) {
            if (nums[l] < nums[r]) {
                tmp[k++] = nums[l++];
            } else {
                tmp[k++] = nums[r++];
            }
        }

        while (l <= mid) {
            tmp[k++] = nums[l++];
        }

        while (r <= right) {
            tmp[k++] = nums[r++];
        }

        for (int i = left; i <= right; i++) {
            nums[i] = tmp[i - left];
        }
    }
```

## 堆排序

