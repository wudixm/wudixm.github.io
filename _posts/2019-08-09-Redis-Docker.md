### docker 部署redis 相关问题
[原文](https://www.missshi.cn/api/view/blog/5a9d35e95b925d6776000000)
拉取Redis官方镜像只需要执行如下命令即可：

docker pull redis
默认情况下，会拉取redis的latest版本。 
Ps：目前对应为4.0.8版本。

运行镜像
基本使用
docker run --name some-redis -d redis:tag
其中，some-redis表示自己定义的容器名称。tag是我们对应的镜像版本，默认为latest。默认情况下，Redis会在容器内启动6379端口。 
同时，我们可以使用如下命令来查看Redis容器的日志：

docker logs some-redis
指定Redis配置文件
当我们需要修改配置文件时，我们只需要在宿主机上创建一个redis.conf文件，并将该文件映射到容器的/usr/local/etc/redis/redis.conf文件，并在命令行启动时指定配置文件即可。

docker run 
    --name some-redis 
    -v /my/own/redis.conf:/usr/local/etc/redis/redis.conf
    -d 
    redis 
    redis-server /usr/local/etc/redis/redis.conf
数据持久化
在使用Redis的容器时，数据持久化有很多种方式，下面我们将描述一种推荐的方式： 
在宿主机上创建一个数据存储目录，并将其映射到容器中的目录中。 
这将数据库文件放在主机系统中的已知位置，并便于主机系统上的工具和应用程序访问文件。 
缺点是用户需要确保目录存在，例如，主机系统上的目录权限和其他安全机制配置正确。 
使用方法如下：

在宿主机中创建一个目录，例如/my/own/datadir。
如下命令启动容器：
docker run 
    --name some-redis 
    -v /my/own/datadir:/data 
    -d 
    redis:tag 
    redis-server --appendonly yes
推荐用法
执行如下命令拉取Mongo镜像：

docker pull redis
创建Mongo专用的文件夹：

cd /mnt
mkdir redis
cd ./redis
mkdir data
执行如下命令启动Redis：

docker run \
    --name redis \
    -p 6379:6379 \
    -v /mnt/redis/data:/data \
    -d \
    redis \
    redis-server --appendonly yes

### redis.conf 文件

forum.topic.util.topic-util-redis=> (car/wcar {:pool {} :spec {:uri "redis://172.17.0.2:6379/1"}} (car/ping))
ExceptionInfo DENIED Redis is running in protected mode because protected mode is enabled, no bind address was specified, no authentication password is requested to clients. In this mode connections are only accepted from the loopback interface. If you want to connect from external computers to Redis you may adopt one of the following solutions: 1) Just disable protected mode sending the command 'CONFIG SET protected-mode no' from the loopback interface by connecting to Redis from the same host the server is running, however MAKE SURE Redis is not publicly accessible from internet if you do so. Use CONFIG REWRITE to make this change permanent. 2) Alternatively you can just disable the protected mode by editing the Redis configuration file, and setting the protected mode option to 'no', and then restarting the server. 3) If you started the server manually just for testing, restart it with the '--protected-mode no' option. 4) Setup a bind address or an authentication password. NOTE: You only need to do one of the above things in order for the server to start accepting connections from the outside.  clojure.core/ex-info (core.clj:4617)

### network

#### 用固定ip
```sh
[root@iz8vbcmllue4daadc7mfm8z forum]# docker inspect -f '{{.NetworkSettings.IPAddress }}' forum_redis_1
172.17.0.3
# 如果用这个命令获取ip，那么如果redis 容器重启，ip 会变。肯定会带来问题

[root@iz8vbcmllue4daadc7mfm8z forum]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
551d2519929e        bridge              bridge              local
3a552d33ccd7        host                host                local
e37bf3747b3d        none                null                local

[root@iz8vbcmllue4daadc7mfm8z forum]# docker network inspect bridge
[
    {
        "Name": "bridge",
        ...
        "Containers": {
            "3fbda535115b670bb1d2768618e2d14769ac0a7b26cbb653fbd4c15b9cc07a84": {
                "Name": "forum_redis_1",
                "EndpointID": "7b8e610594618870b4e4b287e9cd045210306fc18c00f06637f6b640da145bbc",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            },
            "50e5d91e576365425161bca3d86c18d69ae1aadf047bcfdd4c920ae7d999dc66": {
                "Name": "forum_web_1",
                "EndpointID": "fe00dd5d48c79f92b8066d9af38d8f9110c33d7d7e2f2a1225d0c0165056ec25",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        ...
    }
]
```
#### 用networking

如果docker-compose.yml 如下：

```yml
version: '2.1'
services:
  web:
    build:
      context: .
      dockerfile: ./Dockerfile
    command: lein run
    networks:
      - app
    ports:
      - "8081:8081"
    volumes:
      - ".:/mnt"

    networks:
      default:
        ipv4_address: 172.17.0.2

  redis:
    image: redis #docker 镜像
    networks:
      default:
        ipv4_address: 172.17.0.3
    ports:
      - "6379:6379"  #端口号
    command: redis-server /data/redis.conf
    volumes:
      - "/mnt/redis/data:/data"
    # restart: always

```

会报错

ERROR: for forum_web_1  user specified IP address is supported only when connecting to networks with user configured subnets
ERROR: for web  user specified IP address is supported only when connecting to networks with user configured subnets

需要在yml 文件里加上
```yml
networks:
  app:
    external:
      name: app_name

networks:
  app:
    driver: bridge
    ipam:
      config:
        - subnet: 172.18.0.1/16
```

#### overlap space

如果报错是
```sh
ERROR: Pool overlaps with other one on this address space
```
是因为就算container down 了，但是network 还是在运行。所以
1. Actually it seems that a simple sevirce docker restart solved the issue.
2. docker network prune

[root@iz8vbcmllue4daadc7mfm8z forum]# docker network prune
WARNING! This will remove all networks not used by at least one container.
Are you sure you want to continue? [y/N] y
Deleted Networks:
app
forum_default

docker network prune 是删除所有不活跃的network

```sh
[root@iz8vbcmllue4daadc7mfm8z forum]# docker network --help

Usage:	docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks
```
