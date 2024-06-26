---
layout: post
title: Learn Markdown
subtitle:
date: 2016-05-07
author: lewisbase
header-img:
categories: 
    - Markdown
---
对Markdown语言轻度学习做一总结，但由于对HTML和Jekyll不甚熟悉，所以还不知道该如何去修改博文的模板，暂且先用借来的吧。  
从简易教程上学到的主要内容为：  

+ 标题与段落：  
	段落用空行分开（只有空格和tab也是空行）  
	tab缩进表示区块  
	空行或行尾两个空格表示换行  
	>开启引用  
	
+ 强调与修饰：  
	星号、下划线和波浪号  
	*斜体* 或_斜体_  
	**加粗**或__加粗__   
	~~删除线~~  
	==高亮==  

+ 列表：  
	无序列表使用'+', '-', '*' + '空格' + '内容'  
	有序列表使用'数字' + '空格' + '内容'
	1. 注意空格  
	列表中的tab缩进不为区块代码 
 
	嵌套列表缩进需加入段落

+ 链接：  
	有行内和参考两种形式：
	1. 行内\[名称](链接 "标题")  
	2. 参考\[名称][引用]  
		\[引用]: 链接 "标题"
  
	让人痛恨却又不知道那什么来替代的[百度](http://google.com/ "科学上网")  

+ 图片：  
	图片比链接多了一个!在前面，也有行内和参考两种形式  
	![tupian](/public/apple-touch-icon-precomposed.jpg "meiyoutupian")
  
+ 代码：  
	行内代码用\`括起，`#include<iostream>`  
	区块代码用tab缩进

		#include<iostream>  
		int main()  
		{  
			std::cout << "Hello World!\n";  
			return 0;  
		}


暂且先试试这些，日后还需继续学习。 
 
你若盛开，清风自来。


