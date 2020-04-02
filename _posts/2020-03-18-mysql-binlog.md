## binlog 两种查看方法

### 用mysqlbinlog 查看

```sh
mysqlbinlog binlog.002369 --start-datetime="2020-03-18 10:40:00" --stop-datetime="2020-03-18 10:50:00" --base64-output=decode-rows –v
```

### 用mysql 客户端

```mysql
mysql> SHOW BINARY LOGS;

mysql> show binlog events in 'binlog.002369' from 357087310 limit 100;

mysql> show binlog events [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count];

/*
参数解释：
　　a、IN 'log_name':指定要查询的binlog文件名（不指定就是第一个binlog文件）
　　b、FROM pos:指定从哪个pos起始点开始查起（不指定就是从整个文件首个pos点开始算）
　　c、LIMIT【offset】：偏移量(不指定就是0)
　　d、row_count :查询总条数（不指定就是所有行）
*/
```

1、MySQL的二进制日志binlog可以说是MySQL最重要的日志，它记录了所有的DDL和DML语句（除了数据查询语句select）,以事件形式记录，还包含语句所执行的消耗的时间，MySQL的二进制日志是事务安全型的。
　　a、DDL
　　　　----Data Definition Language 数据库定义语言 
　　　　主要的命令有create、alter、drop等，ddl主要是用在定义或改变表(table)的结构,数据类型，表之间的连接和约束等初始工作上，他们大多在建表时候使用。
　　b、DML
　　　　----Data Manipulation Language 数据操纵语言
　　　　主要命令是slect,update,insert,delete,就像它的名字一样，这4条命令是用来对数据库里的数据进行操作的语言
2、mysqlbinlog常见的选项有一下几个：
　　a、--start-datetime：从二进制日志中读取指定等于时间戳或者晚于本地计算机的时间
　　b、--stop-datetime：从二进制日志中读取指定小于时间戳或者等于本地计算机的时间 取值和上述一样
　　c、--start-position：从二进制日志中读取指定position 事件位置作为开始。
　　d、--stop-position：从二进制日志中读取指定position 事件位置作为事件截至
3、一般来说开启binlog日志大概会有1%的性能损耗。
4、binlog日志有两个最重要的使用场景。
　　a、mysql主从复制：mysql replication在master端开启binlog,master把它的二进制日志传递给slaves来达到master-slave数据一致的目的。
　　b、数据恢复：通过mysqlbinlog工具来恢复数据。
　　　　binlog日志包括两类文件：
　　　　1)、二进制日志索引文件(文件名后缀为.index)用于记录所有的二进制文件。
　　　　2)、二进制日志文件(文件名后缀为.00000*)记录数据库所有的DDL和DML(除了数据查询语句select)语句事件。
