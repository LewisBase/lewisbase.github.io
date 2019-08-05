---
layout: post
title: Packmol安装时make过程的错误
subtitle:
date: 2019-07-23
author: lewisbase
header-img:
tags: 
    - Writing
    - Molecular Simulation
---

Packmol是分子模拟中最常用的搭建初始构型的软件。轻便、快捷的特性使其在诸多建模软件中脱颖而出。不过近日在Win10的Linux子系统中安装Packmol时遇到了之前从未遇到的报错。因为自己对Fortran一窍不通，此处先把错误与解决方法记录一下，方便日后研究。

按照标准的安装流程：解压，`./configure`，`make`。但是在`make`过程中报错，错误信息如下：

    random.f90:32:23:
        call random_seed(put=seed)
                       1
    Error: Size of ‘put’ argument of ‘random_seed’ intrinsic at (1) too small (12/33)
    Makefile:182: recipe for target 'random.o' failed
    make: *** [random.o] Error 1

额...似乎是随机数种子的问题，上网搜索了一下，在StackOverflow上找到一个[回答](https://stackoverflow.com/questions/29987816/gfortran-compilation-error-size-of-put-argument-of-random-seed-intrinsic-at)。原来在Fortran中`put`的值要大于等于`size`，答主还提供了一段检测系统`size`的程序：

    program seed_test

        implicit none
        integer n

        n = 0

        call random_seed(size=n)
        write(*,*) 'n = ', n
    end program seed_test

编译运行便可输出自己电脑中的`size`数值：

    $ gfortran -o seed_test seed_test.f90
    $ ./seed_test
    n =           12
    $

我这里是33，好吧，其实在报错信息里已经告诉我了。报错信息指出错误发生在random.f90文件中第32行23列，打开文件发现附近内容为：

    subroutine init_random_number(iseed)
        integer :: i, seed(12), iseed
        do i = 1, 12
            seed(i) = i*iseed
        end do
        call random_seed(put=seed)
        return
    end subroutine init_random_number 

这两个12应该就是问题所在了，修改为33，`make`，安装成功。