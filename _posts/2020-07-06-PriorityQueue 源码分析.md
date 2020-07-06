---
layout: post
title: 'PriorityQueue    源码分析'
tags: [code]
---

创建 PriorityQueue 有多种构造方法，其中可以传入 initialCapacity（如果不传入默认是 11）或 Comparator<? super E> comparator，用于比较元素。还可以传入已有的集合元素。

如果传入一个集合，会新建一个数组，然后把值 copy 进数组，然后进行堆化操作（heapify）

```java
private void initFromCollection(Collection<? extends E> c) {
        initElementsFromCollection(c);
        heapify();
}

private void heapify() {
        for (int i = (size >>> 1) - 1; i >= 0; i--)
            siftDown(i, (E) queue[i]);
  }
```

PriorityQueue 的底层数据结构是一个二叉堆。

##### 添加

add(E e) 调用 offer 方法，所以两个方法是一样的，首先要判断如果原数组大小满了，就要进行扩容。调用 grow()方法。

在 grow() 方法中，如果老数组的 length < 64 就扩容一倍，反之扩容之前的一半大小。然后把原数组拷贝到新数组。


```java
 private void siftUp(int k, E x) {
        if (comparator != null)
            siftUpUsingComparator(k, x);
        else
            siftUpComparable(k, x);
    }
```
在 offer() 方法中，如果插入的不是一个空数组，要调用 siftUp() 方法，传入要插入的位置和值，如果 comparator 不为 null 就用 comparator 进行排序，否则用元素自身的比较器。

```java
private void siftUpUsingComparator(int k, E x) {
        while (k > 0) {
        // （k -1）/ 2 找到父节点的位置
            int parent = (k - 1) >>> 1;
            // 拿到父节点
            Object e = queue[parent];
            if (comparator.compare(x, (E) e) >= 0)
                break;
            queue[k] = e;
            k = parent;
        }
        queue[k] = x;
    }
```
这段逻辑是，先找到父节点，因为数据结构是堆，存储在数组中，所以每个节点的父节点即为该节点的 （index-1）/2 ，每个节点的子节点是 index * 2 +1， index * 2 + 2。 

拿到父节点的值，与要插入的值进行比较，如果该节点大于父节点，直接插入传入的位置，也就是数组的最后。如果该节点小于父节点，就把父节点变为子节点（赋值给 queue[k]），循环直到找到该节点的父节点，然后赋值给对应的位置。


##### 取出

poll 方法，取出 queue[0] 元素，然后将 queue[size-1]插入到 queue[0] 中，然后下沉保持二叉堆特性。

```java
 private void siftDownUsingComparator(int k, E x) {
        int half = size >>> 1;
        // 循环一半的次数就可以了
        while (k < half) {
            int child = (k << 1) + 1;
            // 找到第一个子节点
            Object c = queue[child];
            // 找到右边的子节点
            int right = child + 1;
            // 比较两个子节点，找到更小的那个节点
            if (right < size &&
                comparator.compare((E) c, (E) queue[right]) > 0)
                c = queue[child = right];
                // 子节点中更小的那个跟传入的 x比较
                // 如果 x < c 说明传入的 x 比两个子节点都小，可以直接放在头部
                // 反之进入循环
            if (comparator.compare(x, (E) c) <= 0)
                break;
            queue[k] = c;
            k = child;
        }
        queue[k] = x;
    }
```

首先传入的值是 0 和 最后一个节点，先找到 0 这个位置的两个子节点，然后找到其中最小的子节点 c，用传入的最后一个节点 x 跟  c 作比较，如果 x < c 那么可以直接将 x 放在 0 的位置上。

如果 x > c ，那么把 c 放在 0 的位置，然后循环不断的找子节点，直到找到 x 的子节点，把 x 放入对应的位置。

