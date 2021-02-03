# Mysql参数概念和日志

- Mysql数据库

- - Mysql参数

  - - 概述

    - - Mysql参数又称为系统变量，不同的变量有着不同的作用
      - 可以在启动命令行中或配置文件中对它们进行设置

    - 分类

    - - Global全局系统变量     SET GLOBAL var_name
      - Session会话系统变量    SET SESSION var_name
      - 有些变量既有全局变量又有会话变量，有些变量只有全局变量。全局变量影响服务器的全局操作，会话变量只影响具体客户端连接相关操作。若会话变量未单独设置，则继承自相应全局变量。

    - 设置参数

    - - 命令行方式：SET GLOBAL/SESSION var_name=var_value
      - 配置文件方式：my.cnf(linux) my.ini(windows) [mysqld]下写入var_name=var_value

​			[mysqld]

​				user = mysql    

​				datadir = /data/mysql/data        

​				socket = /data/mysql/tmp/mysql.sockpid-file = /data/mysql/tmp/mysqld.pid  

​				skip_name_resolve = 1

​				max_connections = 2000

​				lower_case_table_names = 1

​				log_timestamps=SYSTEM

​				max_allowed_packet = 32M...

- - - 查询参数

    - - show global variables like %var_name%

  - Mysql日志(https://www.cnblogs.com/plyonfly/p/11469802.html#_label0)

  - - 错误日志error_log

    - - mysql执行过程中的错误信息
      - mysql执行过程中的告警信息
      - event scheduler运行时所产生信息
      - mysq启动和停止过程中产生的信息
      - 主从复制结构中，重从服务器IO复杂线程的启动信息
      - 相关参数

​				log_error：错误日志的存放路径

​				log_warnings：表示是否记录告警信息到错误日志，0表示不记录告警信息，1表示记录告警信息，大于1表示各类告警信息，例如有关网络故障的信息和重新连接信息写入错误日志

- - - 查询日志general_log，默认关闭

    - - 记录所有的数据库执行的命令
      - 相关参数

​				general_log：表示查询日志是否开启，ON表示开启，OFF表示未开启，默认未OFF

​				log_output：表示当查询日志开启以后，以哪种方式存放。FILE表示存放于文件中，TABLE表示存放于表mysql.general_log中(慢查询存放于表mysql.slow_log)，FILE,TABLE表示同时存放于文件和表中，NONE表示不记录日志。

​				general_log_file：表示查询日志存放于文件的路径

- - - - 日志样例				

​				190905 21:51:24	  13 Connect	root@localhost as anonymous on 

​		  						  13 Query	select @@version_comment limit 1

​				190905 21:53:09	  13 Query	create database testdata

​				190905 21:53:18	  13 Query	SELECT DATABASE()

​		  						  13 Init DB	testdata

- - - 慢查询日志slow_query_log，默认关闭

    - - 执行缓慢的语句才会被记录
      - 相关参数

​				slow_query_log：表示查询日志是否开启，ON表示开启，OFF表示未开启，默认OFF

​				slow_query_log_file：表示查询日志存放于文件的路径

​				long_query_time：表示多长时间的查询被认为"慢查询"，默认10s

​				log_queries_not_using_indexes：表示如果运行的sql语句没有使用到索引，也被记录到慢查询日志，OFF表示不记录，ON表示记录，默认OFF

- - - 二进制日志binlog，默认关闭

    - - 记录所有对数据库进行更改的记录，包括操作时间，用时等

​				log_bin：表示二进制日志是否开启，ON表示开启，OFF表示未开启，默认OFF

​				log_bin_basename：二进制日志文件前缀名，二进制日志就记录在该文件中

​				max_binlog_size：二进制日志文件的最大大小，超过此大小，二进制文件会自动滚动

​				log_bin_index：二进制日志文件索引文件名，用于记录所有的二进制文件

​				binlog_format：决定了二进制日志的记录方式，STATEMENT以语句的形式记录，ROW以数据修改的形式记录，MIXED以语句和数据修改混合形式记录

​				sql_log_bin：决定是否对二进制日志进行日志记录，ON表示执行记录，OFF表示不执行记录，默认OFF，这个是会话级别变量可以通SET sql_log_bin = {0|1}来改变该变量值

​				sync_binlog：决定二进制日志写入磁盘时机，如果sync_binlog为0，操作系统来决定什么时候写入磁盘，如果sync_binlog为N（N=1,2,3..），则每N次事务提交后，都立即将内存中的二进制日志写入磁盘，如何选择取决于安全性与性能的权衡

​		