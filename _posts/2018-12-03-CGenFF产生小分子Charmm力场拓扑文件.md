---
layout: post
title: CGenFF产生小分子Charmm力场拓扑文件
subtitle:
date: 2018-12-03
author: lewisbase
header-img:
categories: 
    - Translation
    - Molecular Simulation
---

CGenFF网站现已提供python3版本的charmm2gmx脚本，下载链接如下：

[cgenff_charmm2gmx_py3.py](http://mackerell.umaryland.edu/download.php?filename=CHARMM_ff_params_files/cgenff_charmm2gmx_py3.py)

——2019.10.16. 更新——

CGenFF程序可以直接生成Charmm力场的分子拓扑文件，但使用较为复杂，特此记录一下。

[CGenFF 网站](https://cgenff.umaryland.edu/)  
[CGenFF FAQ](https://cgenff.umaryland.edu/commonFiles/faq.php#morene)

## 使用流程

### 制作CGenFF专属`.str`文件

制作`.mol2`格式的坐标文件，氢原子也包含在内。不推荐使用MS软件，其产生的`.mol2`文件在程序中会出现警告。GView产生的`.mol2`文件需删除两个空行，必要的话可以进行`sed -i 's/Ar/ar' *.mol2`修改大小写。其他软件详见FAQ。

在[CGenFF](https://cgenff.umaryland.edu/initguess/)网站上传`.mol2`文件，此处有三个复选框：

* Guess bond orders from connectivity
* Include parameters that are already in CGenFF
* Use CGenFF legacy v1.0
	

一般情况下都不用选。选项意义如字面所言。选项一重新判断键价，有一定概率出错，如果时用于其他软件的拓扑，不用选择。选项二会将原力场中已包含的信息一并列出，在后续格式转换的过程中可能会出现重复。

`.str`文件中会有一个`penalty`值，该值小于10表明结果较好，在10到50之间则说明需要进行一定的验证，大于50则需要更广泛的验证。

### 转换为Gromacs格式

格式转换需要用到[cgenff_charmm2gmx.py](http://mackerell.umaryland.edu/download.php?filename=CHARMM_ff_params_files/cgenff_charmm2gmx.py)，同时预先下载好Gromacs的[Charmm力场文件](http://mackerell.umaryland.edu/charmm_ff.shtml#gromacs)。

运行改程序的命令为：

	python cegnff_charmm2gmx.py RESNAME drug.mol2 drug.str charmm36.ff

python应该使用2.*版本

`RESNAME`为残基名称，在`.str`文件中见"XXX     0.000!"字段中的XXX

`drug.mol2`为`.mol2`文件

`drug.str`为`.str`文件

`charmm36`为力场文件夹的名称，此处力场版本最好与CGenFF程序中的保持一致，常用为4.0版本

## 其他事项

### `.mol2`文件基本格式

	@<TRIPOS>MOLECULE
	残基名称
	原子个数	键数	残基数
	type: SMALL BIOPOLMER PROTEIN NULEIC_ACID SACCHARIDE
	charges: NO_CHARGES MMFF94_CHARGES USER_CHARGES GSDTELIGER等
	@<TRIPOS>ATOM
	原子信息
	@<TRIPOS>BOND
	成键信息
	...

### 常见错误

Q：转换格式时出现错误："Error in atomgroup.py: read_mol2_coor_only: no. of atoms in mol2 (%d) and top (%d) are unequal"

A：通常由于`.mol2`文件与`.str`文件中的残基名称不一致导致。