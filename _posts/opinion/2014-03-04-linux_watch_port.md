---
layout: post
title: linux 查看端口占用
category: opinion
description: linux 查看端口占用
---

##									linux 查看端口占用


使用命令：比如查看tomcat占用的端口

```linux
	ps -aux | grep tomcat
```
解释：
	ps:显示瞬间行程 (process) 的动态 
	
ps 的参数非常多, 在此仅列出几个常用的参数并大略介绍含义 

```linux
	-A 列出所有的行程 
	-w 显示加宽可以显示较多的资讯 
	-au 显示较详细的资讯 
	-aux 显示所有包含其他使用者的行程
	-e : 在命令执行后显示环境
　　-f : 完整显示输出
```	
发现并没有8080端口的Tomcat进程。
接着使用命令：netstat –apn
查看所有的进程和端口使用情况。发现下面的进程列表，其中最后一栏是PID/Program name 

发现8080端口被PID为9658的Java进程占用。
进一步使用命令：

```linux
	ps -aux | grep java，或者直接：ps -aux | grep pid 查看
```
方法二：

```linux
	直接使用 netstat   -anp   |   grep  portno
	即：netstat –apn | grep 8080
```	
发现被占用的端口自然就可以考虑是不是把它干掉了，
四下看看无人使用，挥动大刀 

```linux
	kill -9 pid 
```
这样你需要的端口就空闲出来了。
	




