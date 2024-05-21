---
layout: post
title: Matplotlib绘图中设置自定义图例
subtitle: 
date: 2024-05-21
author: lewisbase
header-img:
categories: 
    - Python
    - Matplotlib
---

数据分析绘图过程中，往往会出现四组数据两两对比的情况。一直苦恼于不知道该如何在Matplotlib自定义图例的样式和内容，现记录一下条形图中进行图例自定义的方法：

```python
userdefine_label={
   "$\\text{NIHL}_{1-4}$": plt.Rectangle((0, 0), 1, 1, fc="#1f77b4", ec="black", alpha=0.4),
   "$\\text{NIHL}_{346}$": plt.Rectangle((0, 0), 1, 1, fc="#ff7f0e", ec="black", alpha=0.4),
   "experiment group": plt.Rectangle((0, 0), 1, 1, fc="white", ec="black"),
   "control group": plt.Rectangle((0, 0), 1, 1, hatch="///", fc="white", ec="black")
   }
# plt.Rectangle()自定义了图例的样式，hatch为填充样式
   
ax.legend(
    handles=userdefine_label.values(),  # 自定义的样式
    labels=userdefine_label.keys(),     # 自定义的名称
    loc="best",                         # 位置
    fontsize="small"                    # 字体大小
    )
```
