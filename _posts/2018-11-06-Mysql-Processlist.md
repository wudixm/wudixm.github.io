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

