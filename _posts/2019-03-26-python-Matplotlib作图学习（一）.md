---
layout: post
title: Python-Matplotlib作图学习（一）
subtitle:
date: 2019-03-26
author: lewisbase
header-img:
categories: 
    - Python
    - Matplotlib
---

最近重新学习了Matplotlib作图的相关内容，自己对Python的学习还是不系统，只是凭借着C++的习惯在写。许多语法都需要随时查询。所以在这里记录一下程序内容与注意事项。

这是一个简单的曲线图的绘制，涉及内容有Python列表，for循环等，主要是对Matplotlib图表中一些格式的设置。

程序内容如下：

    import numpy as np
    import math
    from pylab import *
    

    PI=3.1415926
    NA=6.02
    
    k=[10.4852,9.21986,10.80252]
    b=[-6.17879,-5.36126,-6.74615]
    name=["A","B","C"]
    linecolor=["black","red","blue"]
    y=[]
    
    x=np.linspace(1,20,5000)
    
    for i in range(3):
        g=((k[i]*x+b[i])/NA)/(PI*(x/2)**2*0.1)
        y.append(g)
    
    figure(figsize=(15,12),dpi=100)
    xlabel("D (nm)",fontsize=40)
    ylabel("$\Delta$$\gamma$ (mN/m)",fontsize=40)
    xlim(0,20)
    ylim(0,12)
    yticks(np.linspace(0,12,4,endpoint=True),fontsize=32)
    xticks(fontsize=32)
    
    for i in range(3):
        plot(x,y[i],label=name[i],linewidth=3,color=linecolor[i])
    
    legend(loc='upper right',fontsize=26,frameon=False)
    ax=gca()
    ax.spines['top'].set_linewidth(5)
    ax.spines['bottom'].set_linewidth(5)
    ax.spines['left'].set_linewidth(5)
    ax.spines['right'].set_linewidth(5)
    tick_params(top='off',right='off',width=5)
    
    show()
    
Python中新建变量不需要声明类型，直接进行赋值初始化便可创建。而对于列表，则可以先创建一个空列表，再通过`.append()`添加变量的方法给其赋值。

对于数组，可以使用Numpy中的`.linspace()`函数创建，一般在函数中输入数组的起点，终点与元素个数即可，可以通过`endpoint`参数指定是否包含终值。也可以使用`range()`生成一个整数数组，常用于for循环中。其参数为起点(默认为0)，终点与步长(默认为1)。

上次对最小二乘法拟合结果作图时简单记录了Matplotlib作图的部分函数的基本用法，这里在此基础上对图片格式进行进一步修饰：

`xlabel ylabel`用来设定x轴与y轴的名称，可以使用`$ $`来使用LaTex格式输入公式或者特殊字符，`fontsize`可用来设置字体大小。

`xlim ylim`用来设定x轴与y轴的区间。

`xticks yticks`用来设定坐标轴上的刻度，通过写入一个数组的方式设定刻度的值，可以直接为函数形式。同样可以用`fontsize设置字号。

`gca()`用于获取当前的Axes对象ax，结合`spines`函数可以分别对设置边框进行设置。

`tick_params`用于设置刻度线显示与否以及其粗细。

最终图片显示效果如下：

![Lines](https://raw.githubusercontent.com/LewisBase/lewisbase.github.io/master/img/_images/2019-03-26-1.png)