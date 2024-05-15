---
layout: post
title: Python-Scipy进行数值积分
subtitle:
date: 2019-03-31
author: lewisbase
header-img:
categories: 
    - Python
    - Scipy
---

Python的Scipy模块中拥有大量的数值计算函数，方便我们快速进行数值计算。

Scipy中的`integrate`模块提供了几种数值积分算法，导入方式为：

    from scipy import integrate

使用`integrate`时，需要先将要进行积分的方程定义为函数。求取一至三重积分的函数分别为：

    integrate.quad(func,a,b,args,full_output)
    integrate.dblquad(func,a,b,gfun,hfun,args,epsabs,epsrel)
    integrate.tplquad(func,a,b,gfun,hfun,qfun,rfun,args,epsabs,epsrel)

以三重积分为例。`func`为运算对象函数，形式为`func(z,y,x)`。`a,b`对应变量`x`的积分区域，`gfun,hfun`对应变量`y`的积分区域，依次类推。

注意`gfun,hfun`等的形式应为函数，其中`gfun,hfun`是自变量为`x`的函数，`qfun,rfun`是自变量为`x,y`的函数。这些函数可以使用`lambda`函数进行定义，形式通常为：

    lambda x,y:x*y
    
如果是常函数，则定义为：

    lambda x:0
    lambda x,y:1
    
* `args`可选，为传递给`func`的格外参数；

* `full_output`可选，非零则返回积分信息的dictionary。如果非零，则还会禁止显示警告消息，并将消息附加到输出元组。

* `epsabs`可选，绝对容差直接传递到内部1-D正交积分。默认值为1.49e-8。

* `epsrel`可选，内部1-D积分的相对容差。默认值为1.49e-8。

运算结果输出一个元组，第一项为积分结果，第二项为绝对误差，此外还有收敛情况等信息。

以下是一个用`integrate.tplquad()`计算立方体晶体的LJ势能的例子：

    #To calculate the LJ potential energy between a cubic and an atom
    #Author: Lewisbase
    #Date: 2019.01.01
    
    from __future__ import division
    import numpy as np
    from scipy import integrate
    
    #Cubic length width hight A,B,C
    A=10
    B=10
    C=10
    
    #Average sigma(s,A) epsilon(e,K) rho(r,A^-3) of the cubic
    s=np.array([4.24,3.84]); e=np.array([208.4541197,69.0609]); r=0.061540355
    
    def LJ_cubic(x,y,z):
        return 4*eps*rho*((sig**2/((xb-x)**2+(yb-y)**2+(zb-z)**2))**6-(sig**2/((xb-x)**2+(yb-y)**2+(zb-z)**2))**3)
    
    lx=50
    ly=50
    lz=100
    dx=lx/101
    dy=ly/101
    dz=lz/201
    x0=lx/2-A/2
    x1=lx/2+A/2
    y0=ly/2-B/2
    y1=ly/2+B/2
    z0=lz/2-C/2
    z1=lz/2+C/2
    for m in np.linspace(0,1,2):	
        for ix in np.linspace(0,100,101):
            for iy in np.linspace(0,100,101):
                for iz in np.linspace(0,200,201):
                    xb=ix*dx
                    yb=iy*dy
                    zb=iz*dz
                    sig=s[m]
                    eps=e[m]
                    rho=r
                    if abs(xb-lx/2)<(A/2) and abs(yb-ly/2)<(B/2) and abs(zb-lz/2)<(C/2) :
                        LJ = 1E100
                    else:
                        LJ=integrate.tplquad(LJ_cubic,
                            z0,
                            z1,
                            lambda y: y0,
                            lambda y: y1,
                            lambda y,x: x0,
                            lambda y,x: x1)
                    LJ=LJ[0]/298.15
                    print "%f    %f    %f    %f\n"%(xb,yb,zb,LJ)

有关数值积分的方法还有高斯积分，龙贝格积分等，自己暂时还没有用到，待到以后再做详细学习。

### 参考资料

[用Python作科学计算](http://bigsec.net/b52/scipydoc/index.html)  
[Scipy.Integrate](https://docs.scipy.org/doc/scipy/reference/tutorial/integrate.html)  
[Scipy.Integrate.quad](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.quad.html#scipy.integrate.quad)  
[Scipy.Integrate.dblquad](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.dblquad.html#scipy.integrate.dblquad)  
[Scipy.Integrate.tplquad](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.tplquad.html#scipy.integrate.tplquad)  