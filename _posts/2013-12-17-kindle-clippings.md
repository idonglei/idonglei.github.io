---
title: Kindle Clippings to Separate Books | 将Kindle笔记按书名整理
layout: post
tags:
  - Kindle
  - Python
  
---
  
这篇属于插播，写完这个老老实实完成后面几篇数据可视化的笔记。不用Kindle的可以忽略本文。

###背景及要解决的问题：

Kindle会把阅读中划出来的句子或做的笔记全放到一个叫My Clippings.txt的文件夹，按句子被标记的时间排序，并没有按不同的书分类。如果同时在读好几本书，那么不同笔记之间很有可能交错存在My Clippings中，给后期整理带来了不便。当然我们可以一条一条手工Ctrl C + Ctrl V，但有没有办法可以一劳永逸呢？答案是肯定的！

我们先了解一下My Clippings.txt中的结构：

* 书名A
* 标记时间
* 空行
* 标记内容
* ==========
* 书名B
* 标记时间
* 空行
* 标记内容
* ==========
* 书名A
* 标记时间
* 空行
* 标记内容
* ==========



结构比较简单，对应思路也不复杂：找到所有不重复的书名，然后遍历所有行，把这个书名对应下的标记时间 + 空行 + 标记内容放到新文件中去。在这个过程中我学到了两个很重要的Python的库及函数：os标准库和with as函数。

os标准库可以使程序在不同平台上都可以有效运行，with as函数也可以方便处理读写文件。关于os标准库可看这里，而with as的用法，这篇文章总结的很好。

###代码：

代码出处在第二行注释中给出，我针对mac做了点小修改，同时结合我的使用笔记习惯进行了调整，并加入了大量注释以利于学习：

	# -*- coding: utf-8 -*-
	#https://github.com/mescoda/kindleSpliter/blob/master/kindleSpliter.py
	 
	import os
	#os标准库
	 
	output_folder = os.path.join('output')
	 
	if not os.path.exists(output_folder):
	        os.mkdir(output_folder) #创建名为output的文件夹存放导出文件
	 
	title_filename = os.path.join(output_folder,' TitleAll.txt')
	 
	with open('My Clippings.txt','r') as clipping_file:
	#with通常用在读取文件操作中，将文件的关闭操作放在exit方法中，这样就不会因忘记释放文件而产生可能出现的错误。
	        line = clipping_file.readlines() #readlines是按行读取，line是一个以每行为元素的list
	        titleline = set() #用set可以遍历所有标题后选出所有不重复的标题
	        for i in range(len(line)):
	                if line[i] == '==========\r\n': #如果line[i]是分割线那一行，window中是\n，mac中是\r\n
	                        titleline.add(line[i-4]) #添加标题
	#至此从My Clippings.txt中找出了所有不重复的书名，存在titleline中
	 
	 
	#在TitleAll.txt文件中写入了所有书名
	with open('title_filename','w') as title_file:
	        for i in titleline:
	                title_file.write(i)
	 
	for j in titleline:
	        x = j.decode('utf-8') #中文解码
	        filename = os.path.join(output_folder, x.strip() +'.txt') #建每本书的文件名
	        with open(filename,'w') as out_file:
	                out_file.write(j) #在每个txt第一行写上书名信息
	                with open('My Clippings.txt','r') as clipping_file:
	                        line = clipping_file.readlines()
	                        for i in range( len(line) ):
	                                if j == line[i]:
	                                        out_file.write(line[i+1]) #每条标记的时间
	                                        out_file.write(line[i+2]) #空行
	                                        out_file.write(line[i+3]) #每条标记的内容