### Spring Boot的Maven插件Spring Boot Maven plugin详解

[原文](https://blog.csdn.net/taiyangdao/article/details/75303181)

Spring Boot的Maven插件（Spring Boot Maven plugin）能够以Maven的方式为应用提供Spring Boot的支持，即为Spring Boot应用提供了执行Maven操作的可能。
Spring Boot Maven plugin能够将Spring Boot应用打包为可执行的jar或war文件，然后以通常的方式运行Spring Boot应用。
Spring Boot Maven plugin的最新版本为2017.6.8发布的1.5.4.RELEASE，要求Java 8, Maven 3.2及以后。

\1. Spring Boot Maven plugin的5个Goals

- spring-boot:repackage，默认goal。在mvn package之后，再次打包可执行的jar/war，同时保留mvn package生成的jar/war为.origin
- spring-boot:run，运行Spring Boot应用
- spring-boot:start，在mvn integration-test阶段，进行Spring Boot应用生命周期的管理
- spring-boot:stop，在mvn integration-test阶段，进行Spring Boot应用生命周期的管理
- spring-boot:build-info，生成Actuator使用的构建信息文件build-info.properties

2. 配置pom.xml文件

**[html]** [view plain](https://blog.csdn.net/taiyangdao/article/details/75303181#) [copy](https://blog.csdn.net/taiyangdao/article/details/75303181#)

1. <build>  
2. ​    <plugins>  
3. ​        <plugin>  
4. ​            <groupId>org.springframework.boot</groupId>  
5. ​            <artifactId>spring-boot-maven-plugin</artifactId>  
6. ​                        <version>1.5.4.RELEASE</version>  
7. ​        </plugin>  
8. ​    </plugins>  
9. </build>  

3.mvn package spring-boot:repackage说明

Spring Boot Maven plugin的最主要goal就是repackage，其在Maven的package生命周期阶段，能够将mvn package生成的软件包，再次打包为可执行的软件包，并将mvn package生成的软件包重命名为*.original。

基于上述配置，对一个生成Jar软件包的项目执行如下命令。

**[html]** [view plain](https://blog.csdn.net/taiyangdao/article/details/75303181#) [copy](https://blog.csdn.net/taiyangdao/article/details/75303181#)

1. mvn package spring-boot:repackage  

可以看到生成的两个jar文件，一个是*.jar，另一个是*.jar.original。

在执行上述命令的过程中，Maven首先在package阶段打包生成*.jar文件；然后执行spring-boot:repackage重新打包，查找Manifest文件中配置的Main-Class属性，如下所示：

**[html]** [view plain](https://blog.csdn.net/taiyangdao/article/details/75303181#) [copy](https://blog.csdn.net/taiyangdao/article/details/75303181#)

1. Manifest-Version: 1.0  
2. Implementation-Title: gs-consuming-rest  
3. Implementation-Version: 0.1.0  
4. Archiver-Version: Plexus Archiver  
5. Built-By: exihaxi  
6. Implementation-Vendor-Id: org.springframework  
7. Spring-Boot-Version: 1.5.3.RELEASE  
8. Implementation-Vendor: Pivotal Software, Inc.  
9. Main-Class: org.springframework.boot.loader.JarLauncher  
10. Start-Class: com.ericsson.ramltest.MyApplication  
11. Spring-Boot-Classes: BOOT-INF/classes/  
12. Spring-Boot-Lib: BOOT-INF/lib/  
13. Created-By: Apache Maven 3.5.0  
14. Build-Jdk: 1.8.0_131  

注意，其中的Main-Class属性值为org.springframework.boot.loader.JarLauncher；

Start-Class属性值为com.ericsson.ramltest.MyApplication。

其中com.ericsson.ramltest.MyApplication类中定义了main()方法，是程序的入口。

通常，Spring Boot Maven plugin会在打包过程中自动为Manifest文件设置Main-Class属性，事实上该属性究竟作用几何，还可以受Spring Boot Maven plugin的配置属性layout控制的，示例如下。

**[html]** [view plain](https://blog.csdn.net/taiyangdao/article/details/75303181#) [copy](https://blog.csdn.net/taiyangdao/article/details/75303181#)

1. <plugin>  
2.   <groupId>org.springframework.boot</groupId>  
3.   <artifactId>spring-boot-maven-plugin</artifactId>  
4.   <version>1.5.4.RELEASE</version>  
5.   <configuration>  
6. ​    <mainClass>${start-class}</mainClass>  
7. ​    <layout>ZIP</layout>  
8.   </configuration>  
9.   <executions>  
10. ​    <execution>  
11. ​      <goals>  
12. ​        <goal>repackage</goal>  
13. ​      </goals>  
14. ​    </execution>  
15.   </executions>  
16. </plugin>  

注意，这里的layout属性值为ZIP。

layout属性的值可以如下：

- JAR，即通常的可执行jar

Main-Class

: org.springframework.boot.loader.JarLauncher

- WAR，即通常的可执行war，需要的servlet容器依赖位于WEB-INF/lib-provided

Main-Class

: org.springframework.boot.loader.warLauncher

- ZIP，即DIR，类似于JAR

Main-Class

: org.springframework.boot.loader.PropertiesLauncher

- MODULE，将所有的依赖库打包（scope为provided的除外），但是不打包Spring Boot的任何Launcher。
- NONE，将所有的依赖库打包，但是不打包Spring Boot的任何Launcher。

4.integration-test阶段中的Spring Boot Maven plugin的start/stop

**[html]** [view plain](https://blog.csdn.net/taiyangdao/article/details/75303181#) [copy](https://blog.csdn.net/taiyangdao/article/details/75303181#)

1. <properties>  
2.   <it.skip>false</it.skip>  
3. </properties>  
4. <build>  
5.   <plugins>  
6. ​    <plugin>  
7. ​      <groupId>org.apache.maven.plugins</groupId>  
8. ​      <artifactId>maven-failsafe-plugin</artifactId>  
9. ​      <configuration>  
10. ​        <skip>${it.skip}</skip>  
11. ​      </configuration>  
12. ​    </plugin>  
13. ​    <plugin>  
14. ​      <groupId>org.springframework.boot</groupId>  
15. ​      <artifactId>spring-boot-maven-plugin</artifactId>  
16. ​      <version>1.5.4.RELEASE</version>  
17. ​      <executions>  
18. ​        <execution>  
19. ​          <id>pre-integration-test</id>  
20. ​          <goals>  
21. ​            <goal>start</goal>  
22. ​          </goals>  
23. ​          <configuration>  
24. ​            <skip>${it.skip}</skip>  
25. ​          </configuration>  
26. ​        </execution>  
27. ​        <execution>  
28. ​          <id>post-integration-test</id>  
29. ​          <goals>  
30. ​            <goal>stop</goal>  
31. ​          </goals>  
32. ​          <configuration>  
33. ​            <skip>${it.skip}</skip>  
34. ​          </configuration>  
35. ​        </execution>  
36. ​      </executions>  
37. ​    </plugin>  
38.   </plugins>  
39. </build>  

注意，it.skip变量用作是否跳过integration-test的标志位。

maven-failsafe-plugin用作integration-test的主要执行目标。

spring-boot-maven-plugin用以为integration-test提供支持。

执行integration-test的Maven命令如下：

mvn verify

或者

mvn verify -Dit.skip=false