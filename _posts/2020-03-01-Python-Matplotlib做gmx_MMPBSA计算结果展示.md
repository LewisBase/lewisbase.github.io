---
layout: post
title: Python-Matplotlib做gmx_MMPBSA计算结果展示
subtitle:
date: 2020-03-01
author: lewisbase
header-img:
categories: 
    - Python
    - Numpy
    - Matplotlib
    - Pandas
    - Molecular Simulation
---


学习使用GROMACS已经很久了，但是一直停留在很初级的应用上面，对高级技巧并不了解。就连做生物体系几乎必做的MMPBSA分析都没有尝试过。乘着这个假期了解了一下MMPBSA，才知道这个计算方法不仅仅是用于蛋白质-配体体系的结合自由能计算，对于任何一个二聚体都是可以的。另外，Jerkwin老师已经发展出了比之前常用的GMXMMPBSA与g_mmpbsa两种脚本/程序更加简单易用的计算脚本——gmx_mmpbsa。gmx_mmpbsa不但没有GROMACS与APBS程序的版本要求，还可以一步运行获得所有结果，大大降低了MMPBSA的学习成本。

Jerkwin老师的[博客](https://jerkwin.github.io/2019/07/31/gmx_mmpbsa%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E/)中有详尽的使用说明以及与其他两种方法计算结果的比对，另外，gmx_mmpbsa脚本的最新版本也在可以他的[github](https://github.com/Jerkwin/gmxtool/tree/master/gmx_mmpbsa)库中找到。

gmx_mmpbsa使用前需安装GRMOMACS与APBS，脚本会自动调用这两个程序进行计算。Ubuntu环境下APBS可以使用`sudo apt install apbs`直接进行安装。在修改脚本内的变量内容时，如果GROMACS与APBS都已添加进了环境变量，则可简写为：`gmx='gmx'`以及`apbs='apbs'`。

脚本运行过程中如果出现某些`awk`函数未定义的错误，那么还需要安装一下`gawk`，使用`sudo apt install gawk`即可。

计算完成后会生成一系列不同结果的文档，这里编写了一个python脚本来进行绘图，顺便复习了一下Pandas与Matplotlib的使用方法。其中有关饼状图的修饰来自于Lemonbit的[知乎专栏文章](https://zhuanlan.zhihu.com/p/26812779)。


    # This script is design to plot the mmpbsa calculate results
    # from Jerkwin's gmx_mmpbsa script
    # Author: Lewisbase
    # Date: 2020.02.29

    import numpy as np 
    import pandas as pd 
    import matplotlib.pyplot as plt 
    from matplotlib import font_manager as fm
    from matplotlib import cm

    def readindata(filename):
        with open(filename) as f:
            text = f.readlines()
        index = []
        data = np.zeros([len(text)-1,len(text[0].split())-1])
        for i in range(1,len(text)):
            index.append(text[i].split()[0])
            for j in range(1,len(text[i].split())):
                if text[i].split()[j] == '|':
                    data[i-1][j-1] = np.nan
                else:
                    data[i-1][j-1]=float(text[i].split()[j])
        columns = text[0].split()[1:]
        dataframe = pd.DataFrame(data=data,index=index,columns=columns)
        return dataframe

    def plot_binding_bar(dataframe):
        '''Plot the bar figure from total MMPBSA data'''
        names = [('Binding Free Energy\nBinding = MM + PB + SA',
                 ['Binding','MM','PB','SA']),
                 ('Molecule Mechanics\nMM = COU + VDW',
                 ['MM','COU','VDW']),
                 ('Poisson Boltzman\nPB = PBcom - PBpro - PBlig',
                 ['PB','PBcom','PBpro','PBlig']),
                 ('Surface Area\nSA = SAcom - SApro - SAlig',
                 ['SA','SAcom','SApro','SAlig'])]
        fig,axs = plt.subplots(2,2,figsize=(8,8),dpi=72)
        axs = np.ravel(axs)

        for ax,(title,name) in zip(axs,names):
            ax.bar(name,dataframe[name].mean(),width=0.5,
                   yerr=dataframe[name].std(),color='rgby')
            for i in range(len(dataframe[name].mean())):
                ax.text(name[i],dataframe[name].mean()[i],
                        '%.3f'%dataframe[name].mean()[i],
                        ha='center',va='center')
            ax.grid(b=True,axis='y')
            ax.set_xlabel('Energy Decomposition Term')
            ax.set_ylabel('Free energy (kJ/mol)')
            ax.set_title(title)
        plt.suptitle('MMPBSA Results')
        plt.tight_layout()
        plt.subplots_adjust(top=0.9)
        plt.savefig('MMPBSA_Results.png')
        plt.show()



    def plot_plot_pie(datas):
        '''Plot the composition curve and pie figure'''
        fig,axs = plt.subplots(2,2,figsize=(8,8),dpi=72)
        axs = np.ravel(axs)

        names = [('Composition of MMPBSA',[0,1,4]),
                 ('Composition of MM',[1,2,3]),
                 ('Composition of PBSA',[4,5,6])]
        labels = ['res_MMPBSA','resMM','resMM_COU','resMM_VDW',
                 'resPBSA','resPBSA_PB','resPBSA_SA']
        colors = ['black','blue','red']
        linestyles = ['-','--',':']
        alphas = [1,0.4,0.4]
        for ax,(title,name) in zip(axs[:-1],names):
            for i in range(len(name)):
                ax.plot(range(datas[name[i]].shape[1]),datas[name[i]].mean(),
                        color=colors[i],alpha=alphas[i],label=labels[name[i]],
                        linestyle=linestyles[i],linewidth=2.5)
            ax.grid(b=True,axis='y')
            ax.set_xlabel('Residues No.')
            ax.set_ylabel('Free Energy Contribution (kJ/mol)')
            ax.legend(loc='best')
            ax.set_title(title)

        explode = np.zeros([datas[0].shape[1]])
        maxposition = np.where(datas[0].mean() == datas[0].mean().abs().max())
        maxposition = np.append(maxposition,np.where(datas[0].mean() == 
                                -1 * datas[0].mean().abs().max()))
        explode[maxposition] = 0.4
        colors = cm.rainbow(np.arange(datas[0].shape[1])/datas[0].shape[1])
        patches, texts, autotexts = axs[-1].pie(datas[0].mean()/datas[0].mean().sum()*100,
                    explode=explode,labels=datas[0].columns,autopct='%1.1f%%',
                    colors=colors,shadow=True,startangle=90,labeldistance=1.1,
                    pctdistance=0.8)
        axs[-1].axis('equal') # Equal aspect ratio ensures that pie is drawn as a circle
        axs[-1].set_title('Composition of MMPBSA')
        # set font size
        proptease = fm.FontProperties()
        proptease.set_size('xx-small')
        # font size include: xx-small,x-small,small,medium,large,x-large.xx-large or numbers
        plt.setp(autotexts,fontproperties=proptease)
        plt.setp(texts,fontproperties=proptease)

        plt.suptitle('MMPBSA Energy Composition')
        plt.tight_layout()
        plt.subplots_adjust(top=0.9)
        plt.savefig('MMPBSA_Energy_Composition.png')
        plt.show()



    if __name__ == '__main__':
        pass
        prefix = input('Input the prefix of the calculate results: \n')
        files = ['MMPBSA','res_MMPBSA','resMM','resMM_COU','resMM_VDW',
                 'resPBSA','resPBSA_PB','resPBSA_SA']
        datas = []
        for file in files:
            filename = prefix + '~' + file + '.dat'
            datas.append(readindata(filename))
        plot_binding_bar(datas[0])
        plot_plot_pie(datas[1:])


运行后会将MMPBSA的计算结果汇总为自由能的柱形图，以及各个残基自由能贡献的线状图与饼状图。数据太多时饼状图的标签会堆叠在一起，暂时还没想到很好的处理办法，图像展示如下：

![Bar](https://raw.githubusercontent.com/LewisBase/LearnPython/master/Plot_MMPBSA/MMPBSA_Results.png)

![Plots and Pie](https://raw.githubusercontent.com/LewisBase/LearnPython/master/Plot_MMPBSA/MMPBSA_Energy_Composition.png)

### 参考资料

[gmx_mmpbsa使用说明](https://jerkwin.github.io/2019/07/31/gmx_mmpbsa%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E/)

[关于matplotlib，你要的饼图在这里](https://zhuanlan.zhihu.com/p/26812779)