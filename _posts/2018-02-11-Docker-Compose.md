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



