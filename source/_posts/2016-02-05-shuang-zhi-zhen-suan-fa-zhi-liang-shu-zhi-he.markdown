---

title: "双指针算法之两数之和"
date: 2016-02-05 09:51:39 +0800
comments: true
categories: 算法
tags: [算法,数据结构]
---

在上一篇中，我们讲到了双指针算法的几种使用场景，这里具体举例，以经典的两数之和为例。

给定一个有序数字的数组和一个目标值，在有序数组中找到两个数字之和等于该目标值，返回包含两个数字的数组，如果没有找到返回包含两个-1值的数组。

```
找到的情况
输入： 
有序数组: [2, 4, 5, 6, 12];  目标值: 10
输出：
[4, 6]
```

```
找不到的情况
输入： 
有序数组: [2, 4, 5, 6, 12];  目标值: 3
输出：
[-1, -1]
```

<!-- more -->
直接代码走起，下面以Java代码为例

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
