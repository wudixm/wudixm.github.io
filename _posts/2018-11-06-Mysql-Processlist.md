## MySQL 连接数

[原文](https://blog.csdn.net/starnight_cbj/article/details/4492555)

### 查看mysql 当前连接数（进程数）

用的命令是`show processlist;`：

```
mysql> show processlist;
+----+-----------------+-----------------+------+---------+--------+------------------------+------------------+
| Id | User            | Host            | db   | Command | Time   | State                  | Info             |
+----+-----------------+-----------------+------+---------+--------+------------------------+------------------+
|  4 | event_scheduler | localhost       | NULL | Daemon  | 493167 | Waiting on empty queue | NULL             |
| 94 | root            | localhost       | NULL | Query   |      0 | starting               | show processlist |
| 95 | root            | localhost:61619 | cm_a | Sleep   |     15 |                        | NULL             |
| 96 | root            | localhost:61620 | cm_a | Sleep   |     15 |                        | NULL             |
+----+-----------------+-----------------+------+---------+--------+------------------------+------------------+
4 rows in set (0.00 sec)

mysql> show processlist;
+----+-----------------+-----------+------+---------+--------+------------------------+------------------+
| Id | User            | Host      | db   | Command | Time   | State                  | Info             |
+----+-----------------+-----------+------+---------+--------+------------------------+------------------+
|  4 | event_scheduler | localhost | NULL | Daemon  | 493180 | Waiting on empty queue | NULL             |
| 94 | root            | localhost | NULL | Query   |      0 | starting               | show processlist |
+----+-----------------+-----------+------+---------+--------+------------------------+------------------+
2 rows in set (0.00 sec)
```

### 第二种方法

```
mysql> use information_schema


mysql> describe PROCESSLIST;
+---------+---------------------+------+-----+---------+-------+
| Field   | Type                | Null | Key | Default | Extra |
+---------+---------------------+------+-----+---------+-------+
| ID      | bigint(21) unsigned | NO   |     |         |       |
| USER    | varchar(32)         | NO   |     |         |       |
| HOST    | varchar(64)         | NO   |     |         |       |
| DB      | varchar(64)         | YES  |     |         |       |
| COMMAND | varchar(16)         | NO   |     |         |       |
| TIME    | int(7)              | NO   |     |         |       |
| STATE   | varchar(64)         | YES  |     |         |       |
| INFO    | varchar(65535)      | YES  |     |         |       |
+---------+---------------------+------+-----+---------+-------+
8 rows in set (0.01 sec)

mysql> select * from PROCESSLIST;
+----+-----------------+-----------+--------------------+---------+--------+------------------------+---------------------------+
| ID | USER            | HOST      | DB                 | COMMAND | TIME   | STATE                  | INFO                      |
+----+-----------------+-----------+--------------------+---------+--------+------------------------+---------------------------+
|  4 | event_scheduler | localhost | NULL               | Daemon  | 494063 | Waiting on empty queue | NULL                      |
| 94 | root            | localhost | information_schema | Query   |      0 | executing              | select * from PROCESSLIST |
+----+-----------------+-----------+--------------------+---------+--------+------------------------+---------------------------+
2 rows in set (0.00 sec)

mysql>
```





### help  show processlist

```
mysql> help show processlist
Name: 'SHOW PROCESSLIST'
Description:
Syntax:
SHOW [FULL] PROCESSLIST

SHOW PROCESSLIST shows you which threads are running. You can also get
this information from the INFORMATION_SCHEMA PROCESSLIST table or the
mysqladmin processlist command. If you have the PROCESS privilege, you
can see all threads. Otherwise, you can see only your own threads (that
is, threads associated with the MySQL account that you are using). If
you do not use the FULL keyword, only the first 100 characters of each
statement are shown in the Info field.

URL: http://dev.mysql.com/doc/refman/8.0/en/show-processlist.html

mysql>
```

### show status

```
命令： show status;

命令：show status like '%下面变量%';

Aborted_clients 由于客户没有正确关闭连接已经死掉，已经放弃的连接数量。 
Aborted_connects 尝试已经失败的MySQL服务器的连接的次数。 
Connections 试图连接MySQL服务器的次数。 
Created_tmp_tables 当执行语句时，已经被创造了的隐含临时表的数量。 
Delayed_insert_threads 正在使用的延迟插入处理器线程的数量。 
Delayed_writes 用INSERT DELAYED写入的行数。 
Delayed_errors 用INSERT DELAYED写入的发生某些错误(可能重复键值)的行数。 
Flush_commands 执行FLUSH命令的次数。 
Handler_delete 请求从一张表中删除行的次数。 
Handler_read_first 请求读入表中第一行的次数。 
Handler_read_key 请求数字基于键读行。 
Handler_read_next 请求读入基于一个键的一行的次数。 
Handler_read_rnd 请求读入基于一个固定位置的一行的次数。 
Handler_update 请求更新表中一行的次数。 
Handler_write 请求向表中插入一行的次数。 
Key_blocks_used 用于关键字缓存的块的数量。 
Key_read_requests 请求从缓存读入一个键值的次数。 
Key_reads 从磁盘物理读入一个键值的次数。 
Key_write_requests 请求将一个关键字块写入缓存次数。 
Key_writes 将一个键值块物理写入磁盘的次数。 
Max_used_connections 同时使用的连接的最大数目。 
Not_flushed_key_blocks 在键缓存中已经改变但是还没被清空到磁盘上的键块。 
Not_flushed_delayed_rows 在INSERT DELAY队列中等待写入的行的数量。 
Open_tables 打开表的数量。 
Open_files 打开文件的数量。 
Open_streams 打开流的数量(主要用于日志记载） 
Opened_tables 已经打开的表的数量。 
Questions 发往服务器的查询的数量。 
Slow_queries 要花超过long_query_time时间的查询数量。 
Threads_connected 当前打开的连接的数量。 
Threads_running 不在睡眠的线程数量。 
Uptime 服务器工作了多少秒。


mysql> show status like '%Handler_write%';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Handler_write | 1094  |
+---------------+-------+
1 row in set (0.01 sec)

mysql>
```

