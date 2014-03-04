---
layout: post
title: linux find 命令
category: opinion
description: linux find 命令
---

##									linux find 命令



Linux下查找文件并删除

```linux	
	find . -name *.png | xargs rm -rf
	查找当前目录下png文件并删除
 
Linux下查找文件并替换文件中的内容
	find ./ -name "*.html" | xargs sed -i "s/要换的字符串/新字符串/g"
```

find命令的参数；
	pathname: find命令所查找的目录路径。例如用.来表示当前目录，用/来表示系统根目录。
	
	-print： find命令将匹配的文件输出到标准输出。
	
	-exec： find命令对匹配的文件执行该参数所给出的shell命令。
	相应命令的形式为'command' { } \;，注意{   }和\；之间的空格。
	
	-ok： 和-exec的作用相同，只不过以一种更为安全的模式来执行该参数所给出的shell命令，
	在执行每一个命令之前，都会给出提示，让用户来确定是否执行。
	


例如：为了用ls -l命令列出所匹配到的文件，可以把ls -l命令放在find命令的-exec选项中

```linux
	# find . -type f -exec ls -l { } \;
	-rw-r--r--    1 root     root        34928 2003-02-25 ./conf/httpd.conf
	-rw-r--r--    1 root     root        12959 2003-02-25 ./conf/magic
	-rw-r--r--    1 root     root          180 2003-02-25 ./conf.d/README
```
上面的例子中，find命令匹配到了当前目录下的所有普通文件，并在-exec选项中使用ls -l命令将它们列出。

在/logs目录中查找更改时间在5日以前的文件并删除它们：

```linux
	$ find logs -type f -mtime +5 -exec rm { } \;
```
记住：在shell中用任何方式删除文件之前，应当先查看相应的文件，一定要小心！当使用诸如mv或rm命令时，
可以使用-exec选项的安全模式。它将在对每个匹配到的文件进行操作之前提示你。
在下面的例子中， find命令在当前目录中查找所有文件名以.LOG结尾、更改时间在5日以上的文件，并删除它们，
只不过在删除之前先给出提示。

```linux
$ find . -name "*.conf" -mtime +5 -ok rm { } \;
< rm ... ./conf/httpd.conf > ? n
按y键删除文件，按n键不删除。
```
任何形式的命令都可以在-exec选项中使用。
在下面的例子中我们使用grep命令。find命令首先匹配所有文件名为“ passwd*”的文件，
例如passwd、passwd.old、passwd.bak，然后执行grep命令看看在这些文件中是否存在一个sam用户。

```linux
	# find /etc -name "passwd*" -exec grep "sam" { } \;
	sam:x:501:501::/usr/sam:/bin/bash
```


在当前目录下查找所有用户具有读、写和执行权限的文件，并收回相应的写权限：

```linux
	# ls -l
	drwxrwxrwx    2 sam      adm          4096 10月 30 20:14 file6
	-rwxrwxrwx    2 sam      adm             0 10月 31 01:01 http3.conf
	-rwxrwxrwx    2 sam      adm             0 10月 31 01:01 httpd.conf
	
	# find . -perm -7 -print | xargs chmod o-w
	# ls -l
	drwxrwxr-x    2 sam      adm          4096 10月 30 20:14 file6
	-rwxrwxr-x    2 sam      adm             0 10月 31 01:01 http3.conf
	-rwxrwxr-x    2 sam      adm             0 10月 31 01:01 httpd.conf
```

用grep命令在所有的普通文件中搜索hostname这个词：

```linux
	# find . -type f -print | xargs grep "hostname"
./httpd1.conf:#     different IP addresses or hostnames and have them handled by the
./httpd1.conf:# VirtualHost: If you want to maintain multiple domains/hostnames on your
```
用grep命令在当前目录下的所有普通文件中搜索hostnames这个词：

```linux
	# find . -name \* -type f -print | xargs grep "hostnames"
./httpd1.conf:#     different IP addresses or hostnames and have them handled by the
./httpd1.conf:# VirtualHost: If you want to maintain multiple domains/hostnameson your
```
注意，在上面的例子中， \用来取消find命令中的*在shell中的特殊含义。
find命令配合使用exec和xargs可以使用户对所匹配到的文件执行几乎所有的命令。

