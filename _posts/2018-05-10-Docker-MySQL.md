### 方法二、docker pull mysql

查找Docker Hub上的mysql镜像

```
runoob@runoob:/mysql$ docker search mysql
NAME                     DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                    MySQL is a widely used, open-source relati...   2529      [OK]       
mysql/mysql-server       Optimized MySQL Server Docker images. Crea...   161                  [OK]
centurylink/mysql        Image containing mysql. Optimized to be li...   45                   [OK]
sameersbn/mysql                                                          36                   [OK]
google/mysql             MySQL server for Google Compute Engine          16                   [OK]
appcontainers/mysql      Centos/Debian Based Customizable MySQL Con...   8                    [OK]
marvambass/mysql         MySQL Server based on Ubuntu 14.04              6                    [OK]
drupaldocker/mysql       MySQL for Drupal                                2                    [OK]
azukiapp/mysql           Docker image to run MySQL by Azuki - http:...   2                    [OK]
...
```

这里我们拉取官方的镜像,标签为5.6

```
runoob@runoob:~/mysql$ docker pull mysql:5.6
```

等待下载完成后，我们就可以在本地镜像列表里查到REPOSITORY为mysql,标签为5.6的镜像。

------

## 使用mysql镜像

### 运行容器

```
runoob@runoob:~/mysql$ docker run -p 3306:3306 --name mymysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/mysql_data -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
21cb89213c93d805c5bacf1028a0da7b5c5852761ba81327e6b99bb3ea89930e
runoob@runoob:~/mysql$ 
```

命令说明：

- **-p 3306:3306：**将容器的3306端口映射到主机的3306端口
- **-v -v $PWD/conf:/etc/mysql/conf.d：**将主机当前目录下的conf/my.cnf挂载到容器的/etc/mysql/my.cnf
- **-v $PWD/logs:/logs：**将主机当前目录下的logs目录挂载到容器的/logs
- **-v $PWD/data:/mysql_data：**将主机当前目录下的data目录挂载到容器的/mysql_data
- **-e MYSQL_ROOT_PASSWORD=123456：**初始化root用户的密码

### 查看容器启动情况

```
runoob@runoob:~/mysql$ docker ps 
CONTAINER ID    IMAGE         COMMAND                  ...  PORTS                    NAMES
21cb89213c93    mysql:5.6    "docker-entrypoint.sh"    ...  0.0.0.0:3306->3306/tcp   mymysql
```