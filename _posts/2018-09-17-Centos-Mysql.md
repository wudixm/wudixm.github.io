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
➜  ~
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

