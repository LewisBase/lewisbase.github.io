---
layout: post
title: 在Win10与Ubuntu双系统中删除Ubuntu
keywords: ["Computer_System"]
description: "Win10 & Linux"
categories: "Computer_System"
tags: ["Win10","Linux"]
---

安装了双系统的电脑中，如果是使用Ubuntu系统自带的grub程序作为启动引导，就不能直接格式化Ubuntu所在的盘，否则硬盘上的MBR内容被擦出，无法成功开机引导。

想要卸载Ubuntu，则需要先删除其MBR内容，在格式化其所在硬盘。对于不同的电脑，修改MBR有不同的方法，所以第一步是判明BIOS启动的类型，方法为：

Win+R打开运行，输入msinfo32，回车查看系统信息
在BIOS模式中显示传统，则启动模式为Legacy BIOS；如果为UEFI，则为UEFI

1.若为Legacy BIOS启动方式  
1.1 下载Mbrfix工具，放置在任意位置，如：`D\Tools\`  
1.2 以管理员身份运行命令提示符，进入MBRfix工具存放目录,例如：

	D:
	cd \Tools

1.3 输入命令

	MbrFix /drive 0 fixmbr /yes
	
1.4 重启电脑，看是否直接进入Win10系统，如是，说明删除成功  

2.若为UEFI启动方式  
2.1 下载Easy UEFI  
[Easy UEFI](https://www.easyuefi.com/index-cn.html)  
2.2 安装UEFI后进入管理EFI启动项功能，删除Ubuntu的EFI分区即可  
2.3 重启电脑，直接进入Win10，成功

在成功修改了MBR内容后，就可以直接将Ubuntu对应的硬盘格式化了~

### 参考资料

[UEFI还是Legacy BIOS？如何确定Windows启动类型](https://www.ithome.com/html/win10/146588.htm)  
[双系统正确卸载Ubuntu系统](https://blog.csdn.net/wae42675/article/details/78821910)  
[UEFI启动Windows10+Ubuntu双系统删除Ubuntu方法](https://blog.csdn.net/tjuyanming/article/details/64929901)
