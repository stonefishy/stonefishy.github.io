---
title: 数据分析中的中位数绝对偏差（MAD）
date: 2020-11-03 09:52:13
tags: [Data Analysis, Math, Python]
categories: Data Analysis
---


**Median Absolute Deviation (MAD)**，即**中位数绝对偏差**，是一种用于衡量数据分散程度或数据集中的异常值的方法。它是一种稳健的统计量，尤其在面对噪声和异常值时，比标准差等方法更加稳健，能更好地反映数据的整体变动。

## MAD的计算步骤

1. **计算数据的中位数（Median）**：
   - 首先，计算数据集的中位数（即所有数据排序后中间的值）。
   - 对于偶数个数据，中位数通常取中间两个数的平均值。

2. **计算每个数据点的绝对偏差（Absolute Deviation）**：
   - 对数据集中的每个数据点，计算它与中位数的差的绝对值：
   {% mathjax %}
   \text{绝对偏差} = |x_i - \text{Median}|
   {% endmathjax %}
   其中，{% mathjax %}x_i{% endmathjax %}是数据集中的每个数据点。

3. **计算这些绝对偏差的中位数**：
   - 然后，对这些绝对偏差值进行排序，找到它们的中位数，这个中位数即为**中位数绝对偏差（MAD）**。

   {% mathjax %}
   \text{MAD} = \text{Median}(|x_i - \text{Median}|) 
   {% endmathjax %}

## 为什么使用MAD？

1. **稳健性**：MAD是一个**稳健统计量**，意味着它对异常值（outliers）不敏感。在数据中存在异常值时，标准差和方差等统计量可能会受到极大影响，而MAD相对更加稳定。

2. **对异常值的敏感度低**：如果数据中有极端值，标准差会被这些异常值拉大，从而影响分散度的度量；而MAD通过使用中位数来减少了这种影响，使其更加适用于不对称或有偏分布的数据。

3. **适用于非正态分布**：标准差的假设是数据分布近似正态，而MAD不依赖于数据分布，因此它能够更好地处理非正态或偏态数据。

## Python示例

还是以学生的成绩来举例，假设有一组数据集如下，我们要计算MAD的值

``` python
import numpy as np

# 假设有一组数据集
data = [80, 85, 90, 92, 85, 88, 75, 78, 92, 95, 100, 85, 92, 88, 85, 200]

# 计算中位数
median = np.median(data)
print("Median:", median)

# 计算每个数据点的绝对偏差
absolute_deviation = np.abs(data - median)
print("Absolute Deviation:", absolute_deviation)

# 计算绝对偏差的中位数
mad = np.median(absolute_deviation)
print("MAD:", mad)
```

输出结果如下：

```
Median: 88.0
Absolute Deviation: [8 3 2 4 3 0 13 10 4 7 12 3 4 0 3 112]
MAD: 4.0
```

## MAD的应用

1. **异常值检测**：MAD通常用于异常值检测。当数据点的绝对偏差大于一定倍数的MAD时，通常可以认为该数据点是一个异常值。比如，如果某个数据点的绝对偏差大于3倍MAD，就可以认为它是异常值。

2. **稳健的标准差替代品**：在数据分析中，如果担心数据中有异常值，使用MAD可以作为标准差的替代品来衡量数据的波动性和离散程度。

3. **数据预处理**：在机器学习和统计建模中，使用MAD可以帮助发现并剔除异常数据，避免它们对模型训练产生负面影响。

4. **回归分析和健壮统计**：MAD也常常应用于健壮回归分析中，用于处理数据中的异常点，尤其是在数据集含有极端值或噪声时。

## 总结

**Median Absolute Deviation (MAD)** 是一种通过中位数衡量数据离散程度的方法，它对异常值的抵抗力较强，适用于含有噪声和异常值的数据。相比于标准差，MAD能够提供更为稳健的分散度度量，广泛应用于异常值检测、稳健回归分析和数据预处理中。
