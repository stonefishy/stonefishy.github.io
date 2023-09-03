---

title: "最短子数组之和"
date: 2016-03-01 17:18:50 +0800
comments: true
categories: 算法
tags: [算法,数据结构]
top: true
---

一个常见的最短子数组算法题是 "Minimum Size Subarray Sum"。在这个问题中，给定一个正整数数组和一个目标值，要找到数组中一个连续子数组，使得子数组的元素和大于等于目标值，并且子数组的长度最小。

例如
```
输入： 
数组: [5, 3, 4, 2, 8, 5];  目标值: 10
输出：
[2, 8]
```
```
输入： 
数组: [5, 3, 4, 2, 8, 5];  目标值: 28
输出：
[]
```
<!-- more -->
```
输入： 
数组: [5, 3, 4, 2, 8, 5];  目标值: 4
输出：
[5]
```

这个问题可以使用双指针来解决窗口的滑动问题，核心代码如下：
```Java
public static int[] minSizeSubArray(int[] numbers, int target) {
    int p1 = 0, p2 = 0; //定义两个指针
    int minLeft = 0; // 最小子数组的左边界
    int minLen = Integer.MAX_VALUE; // 最小子数组的长度
    int sum = 0;    //子数组元素和

    for (p2 = 0; p2 < numbers.length; p2++) {
        sum += numbers[p2]; // 将右指针的元素加入子数组
        while (sum >= target) { // 如果子数组的和大于等于目标值
            int curLen = p2 - p1 + 1; // 获取当前子数组长度
            if (minLen > curLen) { // 如果子数组长度比上次还更小
                minLen = curLen; // 更新子数组最小长度
                minLeft = p1;   // 更新子数组的左边界
            }
            sum -= numbers[p1]; // 将左指针的元素从子数组中移除
            p1++;   //移动左指针，缩小子数组
        }
    }

    if (minLen == Integer.MAX_VALUE) {
        return new int[0];  // 没有找到匹配的子数组
    } else {
        //根据子数组的左边界以及长度，返回子数组
        int[] result = new int[minLen];
        for (int i = 0; i < minLen; i++) {
            result[i] = numbers[i + minLeft];
        }
        return result;
    }
}
```

在上述代码中，我们使用了两个指针 p1 和 p2，分别表示子数组的左右边界。我们在数组上使用滑动窗口来寻找满足条件的最短子数组。当子数组的和大于等于目标值时，我们移动左指针，并更新最小长度。如果发现有更小的数组长度，则更新最小数组长度以及数组的左边界值，当子数组的和不足以满足条件时，我们移动右指针，并继续扩展子数组。

测试结果如下
```Java
public static void main(String[] args) {
    int[] numbers = {5, 3, 4, 2, 8, 5};
    printArray(minSizeSubArray(numbers, 10)); // [2, 8]
    printArray(minSizeSubArray(numbers, 28)); // []
    printArray(minSizeSubArray(numbers, 4)); // [5]
}

private static void printArray(int[] numbers) {
    StringBuilder sb = new StringBuilder();
    sb.append("[");
    for(int i =0; i < numbers.length; i++) {
        sb.append(numbers[i]).append(i == numbers.length - 1 ? "" : ", ");
    }
    sb.append("]");

    System.out.println(sb.toString());
}
```

