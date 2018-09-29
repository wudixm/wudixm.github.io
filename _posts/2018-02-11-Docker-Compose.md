---
title: Docker Compose 入门
excerpt: |
  Docker Compose 入门
category: Docker
feature_image: "https://picsum.photos/2560/600?image=872"
---
### Docker Compose

使用一个Docker-Compose，可以用一个YAML 文件定义一组要启动的容器，以及容器运行时的属性。Docker Compose 称这些容器为“服务”，像这样定义：

```
容器通过某些方法并指定一些运行时的属性来和其他容器产生交互
```

在OS X 上，Docker Toolbox 已经包含了Docker Compose，查看docker compose 的版本：`docker-compose --version`。

### 获取示例应用

为了演示Compose 是如何工作的，使用一个Python Flask 应用作为例子，这个例子使用了以下两个容器：

- 应用容器，运行python 示例程序
- Redis 容器，运行Redis 数据库


### docker-compose up

```
➜  onlyMongo docker-compose -h
Define and run multi-container applications with Docker.

Usage:
  docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
  docker-compose -h|--help

Options:
  -f, --file FILE             Specify an alternate compose file (default: docker-compose.yml)
  -p, --project-name NAME     Specify an alternate project name (default: directory name)
  --verbose                   Show more output
  --no-ansi                   Do not print ANSI control characters
  -v, --version               Print version and exit
  -H, --host HOST             Daemon socket to connect to

  --tls                       Use TLS; implied by --tlsverify
  --tlscacert CA_PATH         Trust certs signed only by this CA
  --tlscert CLIENT_CERT_PATH  Path to TLS certificate file
  --tlskey TLS_KEY_PATH       Path to TLS key file
  --tlsverify                 Use TLS and verify the remote
  --skip-hostname-check       Don't check the daemon's hostname against the name specified
                              in the client certificate (for example if your docker host
                              is an IP address)
  --project-directory PATH    Specify an alternate working directory
                              (default: the path of the Compose file)

Commands:
  build              Build or rebuild services
  bundle             Generate a Docker bundle from the Compose file
  config             Validate and view the Compose file
  create             Create services
  down               Stop and remove containers, networks, images, and volumes
  events             Receive real time events from containers
  exec               Execute a command in a running container
  help               Get help on a command
  images             List images
  kill               Kill containers
  logs               View output from containers
  pause              Pause services
  port               Print the public port for a port binding
  ps                 List containers
  pull               Pull service images
  push               Push service images
  restart            Restart services
  rm                 Remove stopped containers
  run                Run a one-off command
  scale              Set number of containers for a service
  start              Start services
  stop               Stop services
  top                Display the running processes
  unpause            Unpause services
  up                 Create and start containers
  version            Show the Docker-Compose version information
```

### 例子

```
➜  onlyMongo docker-compose -f docker-compose-test.yml up
```

