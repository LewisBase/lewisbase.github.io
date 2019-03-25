---
layout: post
title: GitHubPages博文中图片不显示问题的解决
subtitle:
date: 2019-03-25
author: lewisbase
header-img: /img/home-bg-o.png
tags: 
    - Markdown
---

之前的博文中一直没有插图，主要使因为我不会添加图片……

不清楚图片的引用路径到底应该是什么。最近给博客更换了新模板，顺便学习了一下添加图片的方法。

其实没有什么特别严格的引用路径一般情况下可直接使用github里图片存放位置的网址：

`https://github.com/你的用户名/你的repository仓库名/raw/分支名master/刚你新建的图片文件夹名称/***.png ***.jpg`

根据相应的模板也可以直接提供图片存放位置的绝对路径,这需要相应的几个html文件中提前设置好（= = 这方面我还是不太懂）：

`/刚你新建的图片文件夹名称/***.png ***.jpg`

然而，两种方法在我这里都失效了……

直接使用网址时在github中预览可看到图片，但在网页上依然不显示……

今天突发奇想直接使用了图片本身的链接，发现问题解决了！大概是由于github存放图片的网址上不只有图片信息吧。

现在使用的网址为：

`https://raw.githubusercontent.com/用户名/仓库名/master/文件夹/****.png`

万岁~

![404赛高！](https://raw.githubusercontent.com/LewisBase/lewisbase.github.io/master/img/_images/2019-03-25-1.png)