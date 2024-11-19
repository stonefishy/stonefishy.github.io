---
title: 理解数据分析中的Z-score（标准分数）
date: 2020-09-18 17:21:51
tags: [Data Analysis, Math, Python]
categories: Data Analysis
---

# Z-score（标准分数）

**Z-score**（标准分数）是数据分析中常用的一个标准化指标，用于衡量某个数据点与其所在数据集的平均值之间的偏离程度。具体而言，它表示一个数据点距离数据集均值的标准差数值。Z-score 计算的基本原理是将数据点转化为标准化值，从而便于比较不同数据集中的数据点。

## Z-score的计算公式

{% mathjax %}
Z = \frac{X - \mu}{\sigma}
{% endmathjax %}

其中：
>  {% mathjax %} X {% endmathjax %}：单个数据点的值。

>  {% mathjax %} \mu {% endmathjax %}：数据集的均值（平均值）。

>  {% mathjax %} \sigma {% endmathjax %}：数据集的标准差。

### 解释

- {% mathjax %} X - \mu {% endmathjax %}：表示数据点 {% mathjax %} X {% endmathjax %} 与数据集均值 {% mathjax %} \mu {% endmathjax %} 的偏差，或称为该数据点与均值的差异。
- {% mathjax %} \sigma {% endmathjax %}：标准差，衡量数据集的离散程度。标准差越大，说明数据点之间的差异越大；标准差越小，说明数据点之间的差异越小。

## Z-score 的意义
Z-score 的意义主要有以下几点：

- **Z-score = 0**：表示数据点与均值完全一致。
- **Z-score > 0**：表示数据点大于均值，即位于均值右侧。
- **Z-score < 0**：表示数据点小于均值，即位于均值左侧。
- **Z-score 的绝对值较大**：表示数据点距离均值较远，离群点的可能性较大；例如，{% pbg danger, Z-score 大于 3 或小于 -3 的数据点通常被认为是异常值。%}

## Z-score 的主要应用

1. **标准化数据**：通过 Z-score 可以将数据标准化，使得不同量纲、不同范围的数据具有可比性。例如，在机器学习中，许多算法（如 KNN、SVM）对数据的尺度敏感，因此需要对数据进行标准化处理。
   
2. **异常值检测**：Z-score 可以用来识别数据中的异常值。通常，当 Z-score 的绝对值大于 3 时，认为该数据点为异常值（离群点）。

3. **比较不同数据集**：如果两个数据集具有不同的均值和标准差，可以使用 Z-score 对它们进行比较，便于判断哪个数据点在其各自数据集中的位置更加偏离均值。

## 举个例子

假设我们有一个包含学生成绩的数据集 [80, 85, 90, 92, 85, 88, 75, 78, 92, 95, 100, 85, 92, 88, 85, 200]。我们希望计算数据点 200 的 Z-score，并判断它是否为异常值。

### 数学计算公式

1. **计算均值** {% mathjax %} \mu {% endmathjax %}：
   {% mathjax %}
   \mu = \frac{80 + 85 + 90 + 92 + 85 + 88 + 75 + 78 + 92 + 95 + 100 + 85 + 92 + 88 + 85 + 200}{16} = \frac{1015}{16} = 94.375
   {% endmathjax %}

2. **计算标准差** {% mathjax %} \sigma {% endmathjax %}：
   标准差的计算公式为：
   {% mathjax %}
   \sigma = \sqrt{\frac{(X_1 - \mu)^2 + (X_2 - \mu)^2 + \cdots + (X_n - \mu)^2}{n}}
   {% endmathjax %}
   代入数据集计算：
   {% mathjax %}
   \sigma = \sqrt{\frac{(80 - 94.375)^2 + (85 - 94.375)^2 + (90 - 94.375)^2 + (92 - 94.375)^2 + ....}{16}} =  \approx 27.959513139538036
   {% endmathjax %}

3. **计算 Z-score**：
   {% mathjax %}
   Z = \frac{X - \mu}{\sigma} = \frac{200 - 94.375}{27.959513139538036} = 3.7777839504162842
   {% endmathjax %}


### Python 代码
在Python中，我们可以使用 `numpy` 和 `scipy` 库来计算 Z-score。

``` python
import numpy as np
import pandas as pd
from scipy import stats
import matplotlib.pyplot as plt

# 创建数据集：一组学生成绩，其中包括异常值200
data = [80, 85, 90, 92, 85, 88, 75, 78, 92, 95, 100, 85, 92, 88, 85, 200]
```

计算数据集的均值和标准差和Z-score：

``` python
# 计算均值
mean = np.mean(data)
print("均值 (Mean)：", mean)

# 计算标准差
std = np.std(data)
print("标准差 (Standard Deviation):", std)

# 计算 Z-score
z_scores = stats.zscore(data)
print("200数值的Z-score:", z_scores[-1])
```

输出：
```
均值 (Mean): 94.375
标准差 (Standard Deviation): 27.959513139538036
200数值的Z-score: 3.7777839504162842
```

所以，数据点 200 的 Z-score 为 3.7777839504162842，按照 Z-score 的定义，它位于数据集的右侧，距离均值较远 `（ Z-score 大于3或者小于-3 ）`，因此被认为是异常值。

## 总结

`Z-score` 是衡量数据点相对于数据集平均水平偏差程度的标准化指标，广泛应用于`数据预处理`、`异常值检测`以及`统计分析`等领域。通过 Z-score，我们可以将不同规模、不同单位的数据转换为相同的标准尺度，方便进行比较和进一步分析。
