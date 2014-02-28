---
layout: post
title: Oracle undo 故障解决 (原创)
category: opinion
description: Oracle undo 故障解决 (原创)
---

# Oracle undo 故障解决 (原创)

前几天同事遇到问题，Oracle 服务器突然断电，遭遇Undo 数据库文件损坏。数据库
启动不了。 首先重建了undo2表空间，将表空间重新指定了一下，让数据库启动起来。
然后发现一张关键的表的segment错误。这个时候就只要搞定它了，将被损坏的段找出来，跳过他。

	首先：（1）用spfile 创建pfile，然后修改参数：
			  	#*.undo_tablespace='UNDOTBS1'
			  	#*.undo_management='AUTO'
			  	#*.undo_tablespace
				#*.undo_retention
				undo_management='MANUAL'
				rollback_segments='SYSTEM'
		
			（2）用修改之后的pfile，重启DB
				SQL> STARTUP MOUNT pfile='路径/initorcl.ora' ;
				
				
			（3）删除原来的表空间，创建新的UNDO 表空间
				SQL> drop tablespace undotbs;
				SQL> create undo tablespace undotbs1 datafile '/u01/oradata/undotbs1.dbf' size 10M;
 
			（4）关闭数据库，修改pfile参数，然后用新的pfile创建spfile，在正常启动数据库。
				*.undo_tablespace='UNDOTBS1'
				*.undo_management='AUTO'
				#undo_management='MANUAL'
				#rollback_segments='SYSTEM'
 
 
2.2. 方法二：跳过损坏的segment
 在方法一里面，我们使用了system segment。 
 通过第一部分我们了解到，undo segment 有多个，
 我们可以通过alert log 来查看正在使用的是哪些segment，这些段有可能损坏了。 
 我们只需要把这些损坏的segment 跳过，先正常启动DB，在创建新的UNDO 表空间，在切换一下。
 
	（1）修改pfile，添加参数：
	*._corrupted_rollback_segments='_SYSSMU11$','_SYSSMU12$','_SYSSMU13$'
 	这里是个人自己具体的回退段。
 	可以查询出来： select segment_name,status from dba_rollback_segs;  
 
	（2）用修改之后的pfile启动DB因为跳过了哪些损坏的segment，所以DB 可以正常启动。
 
	（3）创建新的UNDO 表空间，并切换过来
 
	SQL> create undo tablespace undotbs1 datafile '/u01/oradata/undotbs1.dbf' size 10M;
	SQL> alter system set undo_tablespace=undotbs1 scope=both;;
	SQL> drop tablespace undotbs;
 
	（4）修改pfile，创建spfile，并正常启动
	删除：*._corrupted_rollback_segments='_SYSSMU11$','_SYSSMU12$','_SYSSMU13$'
	
####参考<http://blog.csdn.net/tianlesoftware/article/details/6261475>
----
