## Mysql 常用初始化操作

[原文](https://www.jianshu.com/p/d7b9c468f20d)

### Centos检查服务

```
service mysqld status

The service command supports only basic LSB actions (start, stop, restart, try-restart, reload, force-reload, status). For other actions, please try to use systemctl.

service mysqld start/stop
/etc/init.d/mysqld start/stop
```

### mac 上启动服务

```
➜  ~ mysql.server start
Starting MySQL
. SUCCESS!
➜  ~ mysql -u root
```

### 新增用户

```
mysql> CREATE USER 'dev'@localhost IDENTIFIED BY 'dev';
Query OK, 0 rows affected (0.00 sec)

# 再登录
[root@iz8vbcmllue4daadc7mfm8z ~]# mysql -u dev -p
Enter password:
```

### 授权

```
切换到root
mysql -u root

mysql> GRANT all ON *.* TO 'dev'@127.0.0.1;
Query OK, 0 rows affected (0.03 sec)

显示授权
show grants:

+--------------------------------------+
| Grants for @localhost                |
+--------------------------------------+
| GRANT USAGE ON *.* TO ''@'localhost' |
+--------------------------------------+

```

### 显示当前用户

```
mysql> select user, host from mysql.user;
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| dev              | 127.0.0.1 |
| dev              | localhost |
| mysql.infoschema | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
| root             | localhost |
+------------------+-----------+
6 rows in set (0.00 sec)

```

### root

```
mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'root';
Query OK, 0 rows affected (0.03 sec)

mysql>  GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.10 sec)

FLUSH PRIVILEGES;

```

### 最终成功

在docker 里启动的服务，但是访问的数据库在宿主机上，需要在spring boot 的jdbcTemplate 的连接的数据库配置的ip 地址是内网ip，如下

```
spring.datasource.url=jdbc:mysql://172.26.158.219:3306/mm  # 实际外网ip 是47.92.164.205
spring.datasource.username=dev
spring.datasource.password=dev
```

在数据库中的用户配置为：

```
mysql> delete from mysql.user where host = '%';
Query OK, 1 row affected (0.00 sec)

mysql> grant all on mm.* to 'dev'@'%' identified by 'dev' with grant option;
Query OK, 0 rows affected (0.00 sec)

```

可能出现的问题是`com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Access denied for user 'dev'@'%' to database 'mm'`

这个可能是因为有多个用户可以满足匹配的请求链接，如可能匹配了多个如下 dev 配置的host。所以先按照host 为% 删除一下多余的账号，再单独创建一个dev 账号就好了。

```
mysql> select user, host from mysql.user;
+----------+-------------------------+
| user     | host                    |
+----------+-------------------------+
| dev      | %                       |
| admin    | 127.0.0.1               |
| root     | 127.0.0.1               |
| root     | ::1                     |
| username | host                    |
|          | iz8vbcmllue4daadc7mfm8z |
| root     | iz8vbcmllue4daadc7mfm8z |
|          | localhost               |
| root     | localhost               |
+----------+-------------------------+
9 rows in set (0.00 sec)

```

### 升级到8.0

```
sudo yum update mysql-server


[root@localhost OMS]# rpm -ivh  MySQL-server-advanced-5.5.8-1.rhel5.i386.rpm
error: Failed dependencies:
        MySQL conflicts with mysql-5.0.77-4.el5_6.6.x86_64
        MySQL conflicts with mysql-5.0.77-4.el5_6.6.i386

 

后查资料，用rpm -e  confictcompoent -- nodeps解决。如下举例说明：

[root@localhost OMS]# rpm -e mysql-5.0.77-4.el5_6.6.x86_64 --nodeps
[root@localhost OMS]# rpm -e mysql-5.0.77-4.el5_6.6.i386 --nodeps

yum install mysql-community-server 

```

### 启动日志

```
vim /var/log/mysqld.log


[root@iz8vbcmllue4daadc7mfm8z bin]# find / -name *ib_log*
/var/lib/mysql/ib_logfile1
/var/lib/mysql/ib_logfile0
[root@iz8vbcmllue4daadc7mfm8z bin]#



```

### 改密码

```
mysqladmin -u root password 'new-password'

```

### 卸载

```
yum remove mysql mysql-server
mv /var/lib/mysql /var/lib/mysql_old_backup
sudo  yum install mysql mysql-server




systemctl start mysqld.service
```

### 临时密码

```
7、修改临时密码
为了加强安全性，MySQL5.7为root用户随机生成了一个密码，在error log中，关于error log的位置，如果安装的是RPM包，则默认是/var/log/mysqld.log。
只有启动过一次mysql才可以查看临时密码


grep 'temporary password' /var/log/mysqld.log（如果之前安装过MySQL则这里可能会有多个密码，用最后一个，注意这个密码输入时是可以粘贴的）

```

### 改密码出错，遇到policy requirements

[原文](https://serverfault.com/questions/885674/mysql-cant-set-reset-password-for-root-user-properly-centos)

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
mysql>


Your password does not satisfy the current policy requirements.

Assuming that the validate_password plugin is installed, it implements three levels of password checking: LOW, MEDIUM, and STRONG. The default is MEDIUM;

Medium is defined as:

MEDIUM policy adds the conditions that passwords must contain at least 1 numeric character, 1 lowercase character, 1 uppercase character, and 1 special (nonalphanumeric) character.

Use a password that satisfies these requirements, set validate_password_policy to LOW or disable the password validation plugin.


```

### 改密码等级到LOW

```
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+--------+
| Variable_name                        | Value  |
+--------------------------------------+--------+
| validate_password.check_user_name    | ON     |
| validate_password.dictionary_file    |        |
| validate_password.length             | 8      |
| validate_password.mixed_case_count   | 1      |
| validate_password.number_count       | 1      |
| validate_password.policy             | MEDIUM |
| validate_password.special_char_count | 1      |
+--------------------------------------+--------+
7 rows in set (0.11 sec)


mysql> SET GLOBAL validate_password.policy=LOW;
Query OK, 0 rows affected (0.00 sec)

mysql>

因为validate_password.length 为8，所以长度至少要8

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'rootroot';
Query OK, 0 rows affected (0.10 sec)

```

### 修改密码的length

[`validate_password_policy`](https://dev.mysql.com/doc/refman/5.7/en/validate-password-options-variables.html#sysvar_validate_password_policy) affects how `validate_password` uses its other policy-setting system variables, except for checking passwords against user names, which is controlled independently by [`validate_password_check_user_name`](https://dev.mysql.com/doc/refman/5.7/en/validate-password-options-variables.html#sysvar_validate_password_check_user_name).

The [`validate_password_policy`](https://dev.mysql.com/doc/refman/5.7/en/validate-password-options-variables.html#sysvar_validate_password_policy) value can be specified using numeric values 0, 1, 2, or the corresponding symbolic values `LOW`, `MEDIUM`, `STRONG`. The following table describes the tests performed for each policy. For the length test, the required length is the value of the[`validate_password_length`](https://dev.mysql.com/doc/refman/5.7/en/validate-password-options-variables.html#sysvar_validate_password_length) system variable. Similarly, the required values for the other tests are given by other `validate_password_*xxx*`variables.

```
mysql> SET GLOBAL validate_password.length=1;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| validate_password.check_user_name    | ON    |
| validate_password.dictionary_file    |       |
| validate_password.length             | 4     |
| validate_password.mixed_case_count   | 1     |
| validate_password.number_count       | 1     |
| validate_password.policy             | LOW   |
| validate_password.special_char_count | 1     |
+--------------------------------------+-------+
7 rows in set (0.00 sec)


length 改为1 的时候，再查看length 也是4，推测是最小长度是4
```

### 创建dev 用户

```
mysql> CREATE USER 'dev'@'localhost' IDENTIFIED BY 'devdev';
Query OK, 0 rows affected (0.07 sec)

mysql> GRANT ALL PRIVILEGES ON *.* TO 'dev'@'localhost' WITH GRANT OPTION;
Query OK, 0 rows affected (0.02 sec)

mysql> create database mm;
Query OK, 1 row affected (0.07 sec)

mysql> use mm;
Database changed
mysql>
```

### net-working

```
mysql> SHOW VARIABLES LIKE 'skip_networking';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| skip_networking | ON    |
+-----------------+-------+
1 row in set (0.51 sec)

mysql>
```

### 重启mysql

```
service mysqld restart

```

### skip grant tables

```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.65 sec)

mysql>
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.06 sec)

mysql>
```

### mysqld 

```
[root@iz8vbcmllue4daadc7mfm8z quiztest1]# mysqld --user=root --skip-networking=False

这命令不好使
```

### systemctl

#### show-env

```
show-env

[root@iz8vbcmllue4daadc7mfm8z sbin]# systemctl show-environment
LANG=en_US.UTF-8
MYSQLD_OPTS=--user=mysql --skip-grant-tables --skip-networking
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin
```

#### import-env

```
import-env

DB_URL=databus.dev.mysite.io:8080
systemctl import-environment DB_URL
```

#### set / unset env

```
set-env   unset-env

systemctl set-environment MYSQLD_OPTS="--general_log=1"
systemctl unset-environment MYSQLD_OPTS
```

### Bash 查看端口

```bash
[root@iz8vbcmllue4daadc7mfm8z init.d]# netstat -pan | grep mysql
unix  2      [ ACC ]     STREAM     LISTENING     4369082  18609/mysqld         /var/lib/mysql/mysql.sock
unix  2      [ ACC ]     STREAM     LISTENING     4369086  18609/mysqld         /var/run/mysqld/mysqlx.sock
unix  2      [ ]         DGRAM                    4369065  18609/mysqld
[root@iz8vbcmllue4daadc7mfm8z init.d]#

```

### mysql 内部查看端口

```mysql
mysql> show global variables like 'port';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| port          | 3306  |
+---------------+-------+
1 row in set (0.01 sec)

mysql>
```

### service status

```bash
[root@iz8vbcmllue4daadc7mfm8z quiztest1]# systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since 一 2018-12-10 18:30:30 CST; 7s ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 18416 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 18438 (mysqld)
   Status: "SERVER_OPERATING"
   Memory: 394.4M
   CGroup: /system.slice/mysqld.service
           └─18438 /usr/sbin/mysqld --user=mysql --skip-grant-tables --skip-networking=0

12月 10 18:30:25 iz8vbcmllue4daadc7mfm8z systemd[1]: Starting MySQL Server...
12月 10 18:30:30 iz8vbcmllue4daadc7mfm8z systemd[1]: Started MySQL Server.
[root@iz8vbcmllue4daadc7mfm8z quiztest1]#
[r
```

### systemctl daemon-reload

```bash
systemctl daemon-reload


[root@iz8vbcmllue4daadc7mfm8z system]# service mysqld start
Redirecting to /bin/systemctl start mysqld.service
Failed to start mysqld.service: Unit not found.
[root@iz8vbcmllue4daadc7mfm8z system]# systemctl daemon-reload
[root@iz8vbcmllue4daadc7mfm8z system]# service mysqld start
Redirecting to /bin/systemctl start mysqld.service
```

### 重启防火墙

```bash
systemctl enable iptables.service
systemctl start iptables.service

```

### native_password

用Seq Pro 连接阿里mysql 的时候，遇到连接不上：`Cannot connect to MySQL 8.0.4 (caching_sha2_password plugin is missing`，需要用非root 用户去连接，并对用户做如下操作：

```mysql
ALTER USER 'username'@'ip_address' IDENTIFIED WITH mysql_native_password BY 'password';
```

### Establishing SSL error

用Mysql8 的时候，用密码登陆的时候，遇到`WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6...`，解决

```clojure
jdbc:mysql://localhost:3306/Peoples?autoReconnect=true&useSSL=false

This will disable SSL and also suppress the SSL errors.

在Clojure 项目的数据库连接配置中，加入useSSL: false


(def db-spec
  {:classname   "com.mysql.jdbc.Driver"
   :subprotocol "mysql"
   :subname           "//172.26.158.219:3306/mm"
   :user        "dev"
   :password    "devdevdev"
   :useSSL false})
```

### OOM 处理

[原文](https://dzone.com/articles/what-to-do-when-mysql-runs-out-of-memory-troublesh)

Where to Start Troubleshooting MySQL Memory Leaks
Here is what we can start with (assuming it is a Linux server):

Part 1: Linux OS and Config Check
1. Identify the crash by checking MySQL error log and Linux log file (i.e. /var/log/messages or /var/log/syslog). You may see an entry saying that OOM Killer killed MySQL. Whenever MySQL has been killed by OOM "dmesg" also shows details about the circumstances surrounding it.

2. Check the available RAM:

 free -g 

 cat /proc/meminfo 

3. Check what applications are using RAM: “top” or “htop” (see the resident vs virtual memory)

4. Check MySQL configuration: check /etc/my.cnf or in general /etc/my* (including /etc/mysql/* and other files). MySQL may be running with the different my.cnf ( run ps ax| grep mysql )

5. Run  vmstat 5 5 to see if the system is reading/writing via virtual memory and if it is swapping

6. For non-production environments, we can use other tools (like Valgrind, gdb, etc) to examine MySQL usage

Part 2: Checks Inside MySQL
Now we can check things inside MySQL to look for potential MySQL memory leaks.

MySQL allocates memory in tons of places, especially:

Table cache
Performance_schema (run: show engine performance_schema status and look at the last line). That may be the cause for the systems with a small amount of RAM, i.e. 1G or less
InnoDB (run  show engine innodb status and check the buffer pool section, memory allocated for buffer_pool and related caches)
Temporary tables in RAM (find all in-memory tables by running: select * from information_schema.tables where engine='MEMORY')
Prepared statements, when it is not deallocated (check the number of prepared commands via deallocate command by running show global status like  'Com_prepare_sql';show global status like 'Com_dealloc_sql')

The good news is, starting with MySQL 5.7, we have memory allocation in performance_schema. Here is how we can use it:

First, we need to enable collecting memory metrics. Run:

UPDATE setup_instruments SET ENABLED = 'YES'
WHERE NAME LIKE 'memory/%';
2. Run the report from sys schema:

select event_name, current_alloc, high_alloc
from sys.memory_global_by_current_bytes
where current_count > 0;
3. Usually, this will give you the place in code when memory is allocated. It is usually self-explanatory. In some cases, we can search for bugs or we might need to check the MySQL source code.

For example, for the bug where memory was over-allocated in triggers ( https://bugs.mysql.com/bug.php?id=86821) the select shows:

mysql> select event_name, current_alloc, high_alloc from memory_global_by_current_bytes where current_count > 0;
+--------------------------------------------------------------------------------+---------------+-------------+
| event_name                                                                     | current_alloc | high_alloc  |
+--------------------------------------------------------------------------------+---------------+-------------+
| memory/innodb/buf_buf_pool                                                     | 7.29 GiB      | 7.29 GiB    |
| memory/sql/sp_head::main_mem_root                                              | 3.21 GiB      | 3.62 GiB    |
...
The largest chunk of RAM is usually the buffer pool but ~3G in stored procedures seems to be too high.


### innodb_buffer_pool_size

Property	Value
Command-Line Format	--innodb-buffer-pool-size=#
System Variable	innodb_buffer_pool_size
Scope	Global
Dynamic	No
Type	Integer
Default Value	134217728
Minimum Value	5242880
Maximum Value (64-bit platforms)	2**64-1
Maximum Value (32-bit platforms)	2**32-1
The size in bytes of the buffer pool, the memory area where InnoDB caches table and index data. The default value is 134217728 bytes (128MB). The maximum value depends on the CPU architecture; the maximum is 4294967295 (232-1) on 32-bit systems and 18446744073709551615 (264-1) on 64-bit systems. On 32-bit systems, the CPU architecture and operating system may impose a lower practical maximum size than the stated maximum. When the size of the buffer pool is greater than 1GB, setting innodb_buffer_pool_instances to a value greater than 1 can improve the scalability on a busy server.

A larger buffer pool requires less disk I/O to access the same table data more than once. On a dedicated database server, you might set the buffer pool size to 80% of the machine's physical memory size. Be aware of the following potential issues when configuring buffer pool size, and be prepared to scale back the size of the buffer pool if necessary.

Competition for physical memory can cause paging in the operating system.

InnoDB reserves additional memory for buffers and control structures, so that the total allocated space is approximately 10% greater than the specified buffer pool size.

Address space for the buffer pool must be contiguous, which can be an issue on Windows systems with DLLs that load at specific addresses.

The time to initialize the buffer pool is roughly proportional to its size. On instances with large buffer pools, initialization time might be significant.

要是数据机器，可以设置80% 机器内存

### 慢查询

#### 查询状态

执行如下，查看结果，slow_launch_time=# 表示如果建立线程花费了比这个值更长的时间,slow_launch_threads 计数器将增加
```
show VARIABLES like "slow%";

    slow_launch_time	2
    slow_query_log	ON
    slow_query_log_file	/var/lib/mysql/mysql-slow.log

show variables like "long%"; 慢查询时间阈值

    long_query_time	1.000000
```


#### 设置开启慢查询
```
set global slow_query_log = ON;
```

#### 设置时间阈值
```
mysql> show variables like 'long%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set (0.00 sec)

mysql> set long_query_time=1;
Query OK, 0 rows affected (0.00 sec)
```


### explain 

```
explain select * from cv_user_obs where id=11174736
id	select_type	table	partitions	type	possible_keys	key	key_len	ref	rows	filtered	Extra
1	SIMPLE	cv_user_obs	NULL	const	PRIMARY	PRIMARY	4	const	1	100.00	NULL


explain select * from cv_user_obs where name=11174736
id	select_type	table	partitions	type	possible_keys	key	key_len	ref	rows	filtered	Extra
1	SIMPLE	cv_user_obs	NULL	ALL	ix_cv_user_obs_name	NULL	NULL	NULL	14142318	10.00	Using where

```

EXPLAIN字段：

- Table：显示这一行的数据是关于哪张表的

- possible_keys：显示可能应用在这张表中的索引。如果为空，没有可能的索引。可以为相关的域从WHERE语句中选择一个合适的语句

- key：实际使用的索引。如果为NULL，则没有使用索引。MYSQL很少会选择优化不足的索引，此时可以在SELECT语句中使用USE INDEX（index）来强制使用一个索引或者用IGNORE INDEX（index）来强制忽略索引

- key_len：使用的索引的长度。在不损失精确性的情况下，长度越短越好

- ref：显示索引的哪一列被使用了，如果可能的话，是一个常数

- rows：MySQL认为必须检索的用来返回请求数据的行数

- type：这是最重要的字段之一，显示查询使用了何种类型。从最好到最差的连接类型为system、const、eq_reg、ref、range、index和ALL

   - system、const：可以将查询的变量转为常量.  如id=1; id为 主键或唯一键.
   - eq_ref：访问索引,返回某单一行的数据.(通常在联接时出现，查询使用的索引为主键或惟一键)
   - ref：访问索引,返回某个值的数据.(可以返回多行) 通常使用=时发生
   - range：这个连接类型使用索引返回一个范围中的行，比如使用>或< 查找东西，并且该字段上建有索引时发生的情况(注:不一定好于index)
   - index：以索引的顺序进行全表扫描，优点是不用排序,缺点是还要全表扫描
   - ALL：全表扫描，应该尽量避免

- Extra：关于MYSQL如何解析查询的额外信息，主要有以下几种

   - using index：只用到索引,可以避免访问表. 
   - 
   - using where：使用到where来过虑数据. 不是所有的where clause都要显示using where. 如以=方式访问索引.
   - 
   - using tmporary：用到临时表
   - 
   - using filesort：用到额外的排序. (当使用order by v1,而没用到索引时,就会使用额外的排序)
   - 
   - range checked for eache record(index map:N)：没有好的索引.
