---
title: Maven dependency 插件
excerpt: |
  Maven dependency 插件
category: maven
feature_image: "https://picsum.photos/2560/600?image=872"
---

maven-dependency-plugin最大的用途是帮助分析项目依赖，dependency:list能够列出项目最终解析到的依赖列表，dependency:tree能进一步的描绘项目依赖树，dependency:analyze可以告诉你项目依赖潜在的问题，如果你有直接使用到的却未声明的依赖，该目标就会发出警告。maven-dependency-plugin还有很多目标帮助你操作依赖文件，例如dependency:copy-dependencies能将项目依赖从本地Maven仓库复制到某个特定的文件夹下面。

比如查看maven项目的依赖，我们可以用下面命令：

```
mvn dependency:tree
```

```
以Dubbo的 dubbo-demo-provider 为例，我们输入这个命令可以获得下面信息：
```

 

unpack和copy类似，只不过它会把拷来的包解开，例如：

<build> 
​    <plugins> 
​        <plugin> 
​            <artifactId>maven-dependency-plugin</artifactId> 
​            <executions> 
​                <execution> 
​                    <id>unpack</id> 
​                    <phase>package</phase> 
​                    <goals> 
​                        <goal>unpack</goal> 
​                    </goals> 
​                    <configuration> 
​                        <artifactItems> 
​                            <artifactItem> 
​                                <groupId>com.alibaba</groupId> 
​                                <artifactId>dubbo</artifactId> 
​                                <version>${project.parent.version}</version> 
​                                <outputDirectory>${project.build.directory}/dubbo</outputDirectory> 
​                                <includes>META-INF/assembly/**</includes> 
​                            </artifactItem> 
​                        </artifactItems> 
​                    </configuration> 
​                </execution> 
​            </executions> 
​        </plugin> 

…

**dependency:unpack **中常用参数有：

- artifactItems：用来包含声明需要解压的文件元素；
- artifactItem：声明需要解压的文件；
- outputDirectory：定义解压后输出的文件夹；
- includes：定义输出包含的规则；
- excludes：定义输出排除的规则。

Apache Maven Dependency Plugin中比较常用的功能有：

- **dependency:copy**  用来拷贝某一个或多个特定文件到指定目录；
- **dependency:copy-dependencies**  用来拷贝依赖的文件到指定目录；
- **dependency:unpack**  与 copy 类似，但会解压文件；
- **dependency:unpack-dependencies** 与 copy-dependencies 类似，但会解压文件；

 

参考资料：

使用maven命令来分析jar包之间的依赖关系 
<http://outofmemory.cn/code-snippet/10914/maven-command-dependency>



### 自己测试

```
找出该项目所依赖的项目(jar)列表：
mvn dependency:resolve
mvn dependency:analyze(找出项目非直接依赖)

➜  colorv-services git:(bot) ✗ mvn dependency:unpack-dependencies -DoutputDirectory=test
```

### spring-boot 打包不被依赖

```
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <skip>true</skip>
            </configuration>
        </plugin>
    </plugins>
</build>

```

1.spring-boot maven打包，一般pom.xml文件里会加

```
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```

这样打的jar里会多一个目录BOOT-INF。

2.引起问题，程序包不存在。

3.解决办法，如果A子模块包依赖了B子模块包，在B子模块的pom文件，加入

```
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <skip>true</skip>
    </configuration>
</plugin>
```

