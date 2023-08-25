---
layout: post
title: "双指针算法"
date: 2016-01-25 09:09:24 +0800
comments: true
categories: [算法,数据结构]
---

双指针算法（Two Pointer Algorithm）是一种常见的算法技巧，用于解决一些数组或链表等数据结构上的问题。它的核心思想是使用两个指针在数据结构中进行遍历、搜索、比较或操作，以达到解决问题的目的。

通常用于解决以下类型的问题：

**快慢指针：** 在链表中，使用两个指针分别以不同的速度遍历，常用于判断是否存在环，寻找链表的中间节点等。

**左右指针：** 在数组中，使用两个指针从数组的两端向中间移动，可以解决一些需要比较两个元素之间关系的问题，如两数之和、反转数组等。
<!-- more -->
**滑动窗口：** 使用两个指针表示窗口的左右边界，通过移动窗口来实现区间内的计算，例如字符串中的最小覆盖子串、子数组的最大和等问题。

**对撞指针：** 在排序数组中，使用两个指针从两端向中间靠拢，解决一些需要判断两个元素之间关系的问题，如两数之和、三数之和等。

双指针算法的优势在于它可以在O(N)的时间复杂度内解决一些复杂的问题，而不需要使用额外的空间。它在数组和链表等数据结构上都有广泛的应用，常用于解决数组遍历、查找、排序和操作等问题。这种算法可以帮助简化问题的解决过程，提高代码效率。

下面我们来举个列子，如何用双指针快速在有序数组中找到我们想要的数据。
给定一个有序数字的数组和一个目标值，在有序数组中找到两个数字之和等于该目标值，返回包含两个数字的数组，如果没有找到返回包含两个-1值的数组。

比如
> 找到的情况
> 输入： 
有序数组: [2, 4, 5, 6, 12];  目标值: 10
> 输出：
[4, 6]

> 找不到的情况
> 输入： 
有序数组: [2, 4, 5, 6, 12];  目标值: 3
> 输出：
[-1, -1]

``` Java
public class Main {
    public static int[] findNumbers(int[] numbers, int target) {
        int pLeft = 0;  // 左指针初始位置
        int pRight = numbers.length - 1; // 右指针初始位置

        while (pLeft < pRight) {
            int sum = numbers[pLeft] + numbers[pRight];
            if (sum == target) {
                return new int[]{numbers[pLeft], numbers[pRight]}; // 返回匹配的元素
            } else if (sum < target) {
                pLeft++; // 和小于目标值，左指针右移
            } else {
                pRight--; // 和大于目标值，右指针左移
            }
        }

        return new int[]{-1, -1};
    }

    public static void main(String[] args) {
        int[] results = findNumbers(new int[]{2, 4, 5, 6, 12}, 10);
        System.out.printf("[%d, %d]%n", results[0], results[1]); // 输出 [4, 6]

        results = findNumbers(new int[]{2, 4, 5, 6, 12}, 3);
        System.out.printf("[%d, %d]%n", results[0], results[1]); // 输出 [-1, -1]
    }
}
```

在上述代码中，我们使用了两个指针 pLeft 和 pRight 分别指向有序数组的两端。然后，我们计算 numbers[pLeft] 和 numbers[pRight] 的和，并与目标值进行比较。根据和与目标值的大小关系，我们移动左指针或右指针，直到找到符合条件的元素对，或者指针重合没有找到。