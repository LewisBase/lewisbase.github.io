---
layout: post
title: 分子模拟中的Cluster Analysis浅析
subtitle:
date: 2018-09-20
author: lewisbase
header-img:
tags: 
    - Writing
    - Translation
    - Molecular Simulation
---

Cluster Analysis在分子动力学模拟中是一个很常见的概念，但是Cluster这个词的意义很模糊。最开始接触到这个概念的时候以为这是一种分析体系中形成的聚集体数目的方法，后来逐渐发现Cluster Analysis最本初的意思是对动力学模拟产生的构象轨迹进行归类和统计。当然，在配合其他工具的前提下，也能够用来统计聚集体数目。

[Shao](http://pubs.acs.org/doi/abs/10.1021/ct700119m?journalCode=jctcce)等人在一篇介绍Cluster Analysis相关算法的论文中写道：

> This sequence of datas the “MD trajectory”-fully specifies the history of the atomic motions in terms of a sequential time-dependent set of molecular configurations from the MD simulation and the larger set of derived properties calculated from the MD trajectory (such as energies, bond lengths, and angle distributions). These data not only provide insight into the structure, dynamics, and interactions of the biomolecules under study but also can be reused to score putative force field changes, as a set of “good” and “bad” representative structures sampled, and for the development of coarse-grained potentials. Although many of the properties derived from the MD trajectory are rather easy to extract, such as the time evolved root-mean-squared coordinate deviation (RMSd) to the initial structure or various distance and angle time series, some properties are more difficult to extract and may be significantly more time-consuming to evaluate (such as entropies and heat capacities). Further, even with elucidation of these properties, often the inherent relationships among the molecular configurations are hidden in the complexity of the data. One very useful way to expose some of these correlations is to group or cluster molecular configurations into subsets based on the similarity of their conformations (as measured by an appropriate metric). __Clustering is a general data-mining technique that can be applied to any collection of data elements (points) where a function measuring distance between pairs of points is available.__ ___A clustering algorithm partitions the data points into a disjoint collection of sets called clusters. The points in one cluster are ideally closer, or more similar, to each other than to points from other clusters.___ 

简单来说，Clustering是一种通用的数据挖掘技术，可应用于任何可测量点对之间距离的数据元素（点）的集合。Clustering算法将数据点划分为一组组不相交的集合，称为Cluster。相较与其他Cluster中的点，同一Cluster中的点彼此之间更接近或更相似。


You probably want g_clustsize rather than g_cluster. g_cluster is for doing RMSD-based clustering of structures, not computing properties of multiple molecules that form clusters. You're likely getting the error due to differences in RMSD never meeting the threshold. ———— Justin Lemkul [web](https://www.researchgate.net/post/Gromacs_cluster_analysis)


一个VMD的clustering插件 [web](http://physiology.med.cornell.edu/faculty/hweinstein/vmdplugins/clustering/)


GROMACS自带了一个团簇分析工具cluster, 但这个工具主要用于对蛋白的构象进行分类, 支持的通用距离文件为xpm格式, 基本没法使用由其他程序生成的距离矩阵, 除非修改源码. [web](https://jerkwin.github.io/2017/11/11/%E4%BD%BF%E7%94%A8GROMACS%E8%BF%9B%E8%A1%8C%E5%9B%A2%E7%B0%87%E5%88%86%E6%9E%90/)


