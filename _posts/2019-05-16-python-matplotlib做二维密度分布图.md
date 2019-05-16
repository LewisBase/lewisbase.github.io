---
layout: post
title: Python-Matplotlib做二维密度分布图
subtitle:
date: 2019-05-16
author: lewisbase
header-img:
tags: 
    - Python
    - Numpy
    - Malpoltlib

---
之前一直想尝试着用Matplotlib绘制计算结果中的二维密度分布图，这样即省去了许多数据处理的麻烦，也方便直接在Linux系统中直接观察计算的结果。但对Numpy和Maltplotlib的熟练程度含不够，对于程序产生的非矩阵式的数据结构不知道该怎么处理。今天花了一早上仔细研究了一下，终于将这块硬骨头啃下来了。

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
    elif len(sys.argv) == 3:
        script,filename,outputname = sys.argv
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



### 参考资料

[用Python作科学计算](http://bigsec.net/b52/scipydoc/index.html)  
[](https://stackoverflow.com/questions/24791614/numpy-pcolormesh-typeerror-dimensions-of-c-are-incompatible-with-x-and-or-y)
[](https://matplotlib.org/examples/color/colormaps_reference.html)
https://matplotlib.org/gallery/images_contours_and_fields/contourf_demo.html#sphx-glr-gallery-images-contours-and-fields-contourf-demo-py
https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.pcolormesh.html#matplotlib.axes.Axes.pcolormesh
https://doraemonzzz.com/2018/07/15/%E5%88%A9%E7%94%A8matplotlib%E5%BA%93%E4%B8%ADpcolormesh%E4%BD%9C%E5%BD%A9%E5%9B%BE/
https://matplotlib.org/examples/color/colormaps_reference.html