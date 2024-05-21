---
layout: post
title: Python-Matplotlib做二维密度分布图
subtitle:
date: 2019-05-16
author: lewisbase
header-img:
categories: 
    - Python
    - Numpy
    - Matplotlib	
---
之前一直想尝试着用Matplotlib绘制计算结果中的二维密度分布图，这样即省去了许多数据处理的麻烦，也方便直接在Linux系统中观察计算的结果。但对Numpy和Maltplotlib的熟练程度还不够，对于计算程序产生的非矩阵式的数据结构不知道该怎么处理。今天花了一早上仔细研究了一下，终于将这块硬骨头啃下来了。

做colormap图的关键在于矩阵的创建，作为坐标的x与y在形状上为呈转置关系的两个矩阵（即x的行数与y的列数相等，反之亦然），在内容上则应为x以行的形式重复，y以列的形式重复。想要产生这样的两个矩阵，可以通过Numpy中的函数`np.meshgrid(x,y)`来实现。用于表示值变化的z则为一个二维数组，即对于每一对x，y都应存在一个值z\[x\]\[y\]。在一般的做法中，z值可以通过以x，y为自变量的函数产生。

我这里遇到的问题是已有的数据是三个一维数组。相当与将上述的x，y，z矩阵一一对应地平铺开来。这样的数据在Origin中作图十分方便，但在Matplotlib中就得预先处理一下。这个过程实则是将平铺开的数组再压缩回去。

首先要用`np.unique()`函数对x和y的数组进行压缩，得到无重复数值的xn与yn，再使用`np.meshgrid()`函数将xn与yn编织成上文所述的矩阵。对z值的处理需要谨慎一些，需要依次寻找每一个x与y共同对应地唯一的z值。最初的x，y，z是三个等长度的数组，共同的索引编号是它们确保一一对应的锁链。这里可以借助np.argwhere()函数找出x与y数组中某个值的索引，由于x，y是具有重复值的数组，这个索引将是两个包含许多位置的数组。所以我们还需要对两个索引数组用`np.intersect1d()`函数求并集得到唯一的z数组中的索引数。具体的操作应为：

    xn = np.unique(x)
    yn = np.unique(y)
    Xm,Ym = np.meshgrid(xn,yn)
    Zm = []
    for i in yn:
        zm = []
        for j in xn:
            zm_index = np.intersect1d(np.argwhere(y == i),np.argwhere( x == j))
            zm.append(float(z[zm_index])
        Zm.append(zm)

注意这里获取索引数时应当在原始数组中查找，循环遍历时可以使用xn，yn节约成本。另外，由于Xm对应的是行信息，在遍历循环中应该放在内层。

接下来就是作图了，数据处理好之后作图基本也就一行命令的事了。这里尝试了一些格式调整，列举如下：

* `plt.pcolormesh()`与`plt.contourf()`均可用来作二维色彩图，但同样的条件下pcolormesh的效果不如contourf的平滑，所以更倾向与使用contourf
* 分格密度通过`MaxNLocator(nbins=100).tick_values(min,max)`设置，nbins对应分格密度，tick_values为上下限
* 色彩模式通过`plt.cm.get_cmap()`设置，这里选用的'jet'类型与Origin中的很类似。更多的色彩模式可以查看Matplotlib[网站](https://matplotlib.org/examples/color/colormaps_reference.html)
* plt.contourf(Xm,Ym,Zm,levels=,cmap=)为contourf的基本用法，对分格密度和色彩模式进行了设置
* `plt.colorbar()`用来生成colorbar，colorbar的名称，刻度，字体大小可以分别通过`.set_label()`, `.set_ticks()`, `.ax.tick_params()`进行设置

这次在脚本中也加入了argv变量的设置，实现了直接读入文件名进行处理。脚本的具体内容如下：

    # Draw a two-dimensional density map from densxz.dat file
    # Author: lewisbase
    # Date: 2019.05.16
    
    import sys
    import numpy as np 
    import matplotlib.pyplot as plt 
    from matplotlib.colors import BoundaryNorm
    from matplotlib.ticker import MaxNLocator
    
    ####################################################################################################
    
    if len(sys.argv) < 2:
        print('No Action specified.')
        sys.exit()
    elif len(sys.argv) == 2:
        if sys.argv[1].startswith('--'):
            option = sys.argv[1][2:]
            if option == 'version':
                print('''
                Densmap version 1.0.0
                Author: lewisbase
                Date: 2019.05.16''')
                sys.exit()
            elif option == 'help':
                print('''
                This script is used to draw a two-domensional density map from densxz.dat.
                Options include:
                --version: Prints the version information
                --help:    Prints the help information
                Usage:
                python Densmap.py filename outputname bar
                bar is an alternative parameter, input it will generate the colorbar''')
                sys.exit()
            else:
                print('Unknow option!')
                sys.exit()
        else:
            print('We need a --option or two filenames at least!')
            sys.exit()
    elif len(sys.argv) == 4:
        script,filename,outputname,bar = sys.argv
    else:
        print('Too many parameters!')
        sys.exit()
    
    ######################################################################################################
    
    with open(filename,'r') as f:
        text = f.readline().split()
    
    if len(text) < 3:
        raise Exception('The input densxz.dat file is corrupted!')
    elif len(text) >= 3:
        xo,zo = np.loadtxt(filename,usecols=(0,1),unpack=True)
        do = np.zeros(len(xo))
        for column in range(2,len(text)):
            dc = np.loadtxt(filename,usecols=(column))
            do += dc
    xo /= 10
    zo /= 10
    do *= 1661.129
    
    xn = np.unique(xo)
    zn = np.unique(zo)
    xm,zm = np.meshgrid(xn,zn)
    
    Dm = []
    for j in zn:
        dm = []
        for i in xn:
            do_index=np.intersect1d(np.argwhere(xo == i),np.argwhere(zo == j))
            dm.append(float(do[do_index]))
        Dm.append(dm)
    
    #######################################################################################################
    
    plt.figure(figsize=(12,12),dpi=100,frameon=True)
    # set the grids density
    levels = MaxNLocator(nbins=100).tick_values(np.min(Dm),np.max(Dm))
    cm = plt.cm.get_cmap('jet')
    #nm = BoundaryNorm(levels,ncolors=cm.N,clip=True)
    
    #plt.pcolormesh(xm,zm,Dm,cmap=cm,norm=nm)
    # contourf method is much smoother than pcolormesh!
    plt.contourf(xm,zm,Dm,levels=levels,cmap=cm)
    if bar == 'bar':
        cbar = plt.colorbar()
        cbar.set_label('Unit: mol/L',rotation=-90,va='bottom',fontsize=40)
        cbar.set_ticks([0,2,4,6,8,10])
        # set the font size of colorbar
        cbar.ax.tick_params(labelsize=32) 
    
    plt.xlabel('X (nm)',fontsize=40)
    plt.ylabel('Z (nm)',fontsize=40)
    plt.xticks(fontsize=32)
    plt.yticks(fontsize=32)
    plt.tight_layout()
    plt.savefig(outputname,dpi=300)
    plt.show()

最终得到的图像结果如下：

![Densmap](https://raw.githubusercontent.com/LewisBase/lewisbase.github.io/master/assets/_images/2019-05-16-1.png)

### 参考资料

[用Python作科学计算](http://bigsec.net/b52/scipydoc/index.html)  
[Stackoverflow](https://stackoverflow.com/questions/24791614/numpy-pcolormesh-typeerror-dimensions-of-c-are-incompatible-with-x-and-or-y)  
[Matplotlib-contours](https://matplotlib.org/gallery/images_contours_and_fields/contourf_demo.html#sphx-glr-gallery-images-contours-and-fields-contourf-demo-py)  
[Matplotlib-pcolormesh](https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.pcolormesh.html#matplotlib.axes.Axes.pcolormesh)  
[利用matplotlib库中pcolormesh作彩图](https://doraemonzzz.com/2018/07/15/%E5%88%A9%E7%94%A8matplotlib%E5%BA%93%E4%B8%ADpcolormesh%E4%BD%9C%E5%BD%A9%E5%9B%BE/)
