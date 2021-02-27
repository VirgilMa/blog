---
layout: post
title: Interview Tips
---

## 树的遍历

- 先序遍历：访问根结点 - 先序遍历左子树 - 先序遍历右子树。
- 中序遍历：中序遍历左子树 - 访问根结点 - 中序遍历右子树。
- 后序遍历：后序遍历左子树 - 后序遍历右子树 - 访问根结点。

### 关联数组

与 map 类似，使用任意值作为索引(key)来存储与获取 value；

- clone()只复制基本的数据类型、引用

## 堆排序

java 实现

```java
    /**
     * 堆排序
     */
    private static void heapSort(int[] arr) {
        // 将待排序的序列构建成一个堆
        for (int i = arr.length / 2; i >= 0; i--){
            heapAdjust(arr, i, arr.length);
        }

        // 逐步将每个最大值的根节点与末尾元素交换，并且再调整二叉树
        for (int i = arr.length - 1; i > 0; i--) {
            swap(arr, 0, i); // 交换
            heapAdjust(arr, 0, i); // 调整
        }
    }

    /**
     * 构建堆的过程
     * @param arr 需要排序的数组
     * @param i 需要构建堆的根节点的序号
     * @param n 数组的长度
     */
    private static void heapAdjust(int[] arr, int i, int n) {
        int child;
        int father;
        for (father = arr[i]; 2 * i + 1 < n; i = child) { // 以存在子树为条件
            child = 2 * i + 1; // 获得左子树

            // 如果左子树小于右子树，则需要比较右子树和父节点
            if (child < n - 1 && arr[child] < arr[child + 1]) {
                child++; // 指向右子树
            }

            // 如果父节点小于孩子结点，则需要交换
            if (father < arr[child]) {
                arr[i] = arr[child];
            } else {
                break; // 不需要调整
            }
        }
        arr[i] = father;
    }

    private static void swap(int[] arr, int index1, int index2) {
        int tmp = arr[index1];
        arr[index1] = arr[index2];
        arr[index2] = tmp;
    }
```
