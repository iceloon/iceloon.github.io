---
layout: post
title: 双系统重装其一时恢复引导
description: 
category: blog
---

###1、重建Boot Loader
以管理员登陆系统xp,运行cmd,插入win7盘并定位到光驱盘符,运行命令：

	$ x:\boot\bootsect -nt60 all

###2、在命令界面中定位到＂X:\windows\system32＂目录下（X为win7的系统目录分区）,
运行命令
	$ bcdedit -create {ntldr} -d ＂WinXP＂

###3、继续命令

	$ bcdedit -set {ntldr} device partition=X:

（ntldr是WinXP的启动文件,X为ntldr文件所在的分区）

###4、运行命令
	
	$ bcdedit -set {ntldr} path \ntldr

###5、运行命令

	$ bcdedit -displayorder {ntldr} -addlast

###6、重启计算机！