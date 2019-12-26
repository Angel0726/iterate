---
title: 八大排序算法的 Python 实现
toc: true
date: 2019-10-19
---
# 八大排序算法的 Python 实现



## 排序算法

<center>

![mark](http://images.iterate.site/blog/image/20191019/69Cw2iIOaRpg.png?imageslim)

</center>



### 直接插入排序

- 时间复杂度：O(n²)
- 空间复杂度：O(1)
- 稳定性：稳定

```py
def insert_sort(array):
    for i in range(len(array)):
        for j in range(i):
            if array[i] < array[j]:
                array.insert(j, array.pop(i))
                break
    return array
```



### 希尔排序

- 时间复杂度：O(n)
- 空间复杂度：O(n√n)
- 稳定性：不稳定

```py
def shell_sort(array):
    gap = len(array)
    while gap > 1:
        gap = gap // 2
        for i in range(gap, len(array)):
            for j in range(i % gap, i, gap):
                if array[i] < array[j]:
                    array[i], array[j] = array[j], array[i]
    return array
```



### 简单选择排序

- 时间复杂度：O(n²)
- 空间复杂度：O(1)
- 稳定性：不稳定

```py
def select_sort(array):
    for i in range(len(array)):
        x = i  # min index
        for j in range(i, len(array)):
            if array[j] < array[x]:
                x = j
        array[i], array[x] = array[x], array[i]
    return array
```



### 堆排序

- 时间复杂度：O(nlog₂n)
- 空间复杂度：O(1)
- 稳定性：不稳定

```py
def heap_sort(array):
    def heap_adjust(parent):
        child = 2 * parent + 1  # left child
        while child < len(heap):
            if child + 1 < len(heap):
                if heap[child + 1] > heap[child]:
                    child += 1  # right child
            if heap[parent] >= heap[child]:
                break
            heap[parent], heap[child] = \
                heap[child], heap[parent]
            parent, child = child, 2 * child + 1

    heap, array = array.copy(), []
    for i in range(len(heap) // 2, -1, -1):
        heap_adjust(i)
    while len(heap) != 0:
        heap[0], heap[-1] = heap[-1], heap[0]
        array.insert(0, heap.pop())
        heap_adjust(0)
    return array
```



### 冒泡排序

- 时间复杂度：O(n²)
- 空间复杂度：O(1)
- 稳定性：稳定

```py
def bubble_sort(array):
    for i in range(len(array)):
        for j in range(i, len(array)):
            if array[i] > array[j]:
                array[i], array[j] = array[j], array[i]
    return array
```



### 快速排序

- 时间复杂度：O(nlog₂n)
- 空间复杂度：O(nlog₂n)
- 稳定性：不稳定

```py
def quick_sort(array):
    def recursive(begin, end):
        if begin > end:
            return
        l, r = begin, end
        pivot = array[l]
        while l < r:
            while l < r and array[r] > pivot:
                r -= 1
            while l < r and array[l] <= pivot:
                l += 1
            array[l], array[r] = array[r], array[l]
        array[l], array[begin] = pivot, array[l]
        recursive(begin, l - 1)
        recursive(r + 1, end)

    recursive(0, len(array) - 1)
    return array
```



### 归并排序

- 时间复杂度：O(nlog₂n)
- 空间复杂度：O(1)
- 稳定性：稳定

```py
def merge_sort(array):
    def merge_arr(arr_l, arr_r):
        array = []
        while len(arr_l) and len(arr_r):
            if arr_l[0] <= arr_r[0]:
                array.append(arr_l.pop(0))
            elif arr_l[0] > arr_r[0]:
                array.append(arr_r.pop(0))
        if len(arr_l) != 0:
            array += arr_l
        elif len(arr_r) != 0:
            array += arr_r
        return array

    def recursive(array):
        if len(array) == 1:
            return array
        mid = len(array) // 2
        arr_l = recursive(array[:mid])
        arr_r = recursive(array[mid:])
        return merge_arr(arr_l, arr_r)

    return recursive(array)
```



### 基数排序

- 时间复杂度：O(d(r+n))
- 空间复杂度：O(rd+n)
- 稳定性：稳定

```py
def radix_sort(array):
    bucket, digit = [[]], 0
    while len(bucket[0]) != len(array):
        bucket = [[], [], [], [], [], [], [], [], [], []]
        for i in range(len(array)):
            num = (array[i] // 10 ** digit) % 10
            bucket[num].append(array[i])
        array.clear()
        for i in range(len(bucket)):
            array += bucket[i]
        digit += 1
    return array
```



## 速度比较

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 数据生成函数

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 显示执行时间

**如果数据量特别大，采用分治算法的快速排序和归并排序，可能会出现递归层次超出限制的错误。**

解决办法：导入 sys 模块（import sys），设置最大递归次数（sys.setrecursionlimit(10 ** 8)）。

```py
@exectime
def bubble_sort(array):
    for i in range(len(array)):
        for j in range(i, len(array)):
            if array[i] > array[j]:
                array[i], array[j] = array[j], array[i]
    return array


array = load_random_array()
print(bubble_sort(array) == sorted(array))
```

↑ 示例：测试直接插入排序算法的运行时间，@exectime 为执行时间装饰器。

### 算法执行时间

![img](https://images2015.cnblogs.com/blog/875028/201705/875028-20170510234041066-399934425.png)

### 算法速度比较

![img](https://images2015.cnblogs.com/blog/875028/201705/875028-20170511003402707-2055171743.png)

![img](https://images2015.cnblogs.com/blog/875028/201705/875028-20170511000356644-56652327.png)




# 相关

- [Python 八大排序算法速度比较](https://www.cnblogs.com/woider/p/6835466.html)
