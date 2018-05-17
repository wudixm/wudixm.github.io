---
title: Spring Cloud
excerpt: |
  Spring Cloud
category: Spring Cloud
feature_image: "https://picsum.photos/2560/600?image=872"
---
## 11. Developing Your First Spring Boot Application

from: [SpringCloud](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-introducing-spring-boot)

This section describes how to develop a simple “Hello World!” web application that highlights some of Spring Boot’s key features. We use Maven to build this project, since most IDEs support it.

| ![[Tip]](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/images/tip.png) |
| ------------------------------------------------------------ |
| The [spring.io](https://spring.io/) web site contains many “Getting Started” [guides](https://spring.io/guides) that use Spring Boot. If you need to solve a specific problem, check there first.You can shortcut the steps below by going to [start.spring.io](https://start.spring.io/) and choosing the "Web" starter from the dependencies searcher. Doing so generates a new project structure so that you can [start coding right away](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-first-application-code). Check the [Spring Initializr documentation](https://github.com/spring-io/initializr) for more details. |

Before we begin, open a terminal and run the following commands to ensure that you have valid versions of Java and Maven installed:

```
$ java -version
java version "1.8.0_102"
Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)
```

```
$ mvn -v
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T16:41:47+00:00)
Maven home: /usr/local/Cellar/maven/3.3.9/libexec
Java version: 1.8.0_102, vendor: Oracle Corporation
```

| ![[Note]](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/images/note.png) |
| ------------------------------------------------------------ |
| This sample needs to be created in its own folder. Subsequent instructions assume that you have created a suitable folder and that it is your current directory. |

## 11.1 Creating the POM

We need to start by creating a Maven `pom.xml` file. The `pom.xml` is the recipe that is used to build your project. Open your favorite text editor and add the following:

```Xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>myproject</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.0.RELEASE</version>
	</parent>

	<!-- Additional lines to be added here... -->

</project>
```

The preceding listing should give you a working build. You can test it by running `mvn package` (for now, you can ignore the “jar will be empty - no content was marked for inclusion!” warning).

| ![[Note]](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/images/note.png) |
| ------------------------------------------------------------ |
| At this point, you could import the project into an IDE (most modern Java IDEs include built-in support for Maven). For simplicity, we continue to use a plain text editor for this example. |

## 11.2 Adding Classpath Dependencies

Spring Boot provides a number of “Starters” that let you add jars to your classpath. Our sample application has already used `spring-boot-starter-parent` in the `parent` section of the POM. The `spring-boot-starter-parent` is a special starter that provides useful Maven defaults. It also provides a[`dependency-management`](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-dependency-management) section so that you can omit `version` tags for “blessed” dependencies.

Other “Starters” provide dependencies that you are likely to need when developing a specific type of application. Since we are developing a web application, we add a`spring-boot-starter-web` dependency. Before that, we can look at what we currently have by running the following command:

```
$ mvn dependency:tree

[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT
```

The `mvn dependency:tree` command prints a tree representation of your project dependencies. You can see that `spring-boot-starter-parent` provides no dependencies by itself. To add the necessary dependencies, edit your `pom.xml` and add the `spring-boot-starter-web` dependency immediately below the `parent` section:

```Xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>
```

If you run `mvn dependency:tree` again, you see that there are now a number of additional dependencies, including the Tomcat web server and Spring Boot itself.

## 11.3 Writing the Code

To finish our application, we need to create a single Java file. By default, Maven compiles sources from `src/main/java`, so you need to create that folder structure and then add a file named `src/main/java/Example.java` to contain the following code:

```Java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

	@RequestMapping("/")
	String home() {
		return "Hello World!";
	}

	public static void main(String[] args) throws Exception {
		SpringApplication.run(Example.class, args);
	}

}
```

Although there is not much code here, quite a lot is going on. We step through the important parts in the next few sections.

### 11.3.1 The @RestController and @RequestMapping Annotations

The first annotation on our `Example` class is `@RestController`. This is known as a *stereotype* annotation. It provides hints for people reading the code and for Spring that the class plays a specific role. In this case, our class is a web `@Controller`, so Spring considers it when handling incoming web requests.

The `@RequestMapping` annotation provides “routing” information. It tells Spring that any HTTP request with the `/` path should be mapped to the `home` method. The`@RestController` annotation tells Spring to render the resulting string directly back to the caller.

| ![[Tip]](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/images/tip.png) |
| ------------------------------------------------------------ |
| The `@RestController` and `@RequestMapping` annotations are Spring MVC annotations. (They are not specific to Spring Boot.) See the [MVC section](https://docs.spring.io/spring/docs/5.0.4.RELEASE/spring-framework-reference/web.html#mvc)in the Spring Reference Documentation for more details. |

### 11.3.2 The @EnableAutoConfiguration Annotation

The second class-level annotation is `@EnableAutoConfiguration`. This annotation tells Spring Boot to “guess” how you want to configure Spring, based on the jar dependencies that you have added. Since `spring-boot-starter-web` added Tomcat and Spring MVC, the auto-configuration assumes that you are developing a web application and sets up Spring accordingly.

**Starters and Auto-Configuration**

Auto-configuration is designed to work well with “Starters”, but the two concepts are not directly tied. You are free to pick and choose jar dependencies outside of the starters. Spring Boot still does its best to auto-configure your application.

### 11.3.3 The “main” Method

The final part of our application is the `main` method. This is just a standard method that follows the Java convention for an application entry point. Our main method delegates to Spring Boot’s `SpringApplication` class by calling `run`. `SpringApplication` bootstraps our application, starting Spring, which, in turn, starts the auto-configured Tomcat web server. We need to pass `Example.class` as an argument to the `run` method to tell `SpringApplication` which is the primary Spring component. The `args` array is also passed through to expose any command-line arguments.

## 11.4 Running the Example

At this point, your application should work. Since you used the `spring-boot-starter-parent` POM, you have a useful `run` goal that you can use to start the application. Type `mvn spring-boot:run` from the root project directory to start the application. You should see output similar to the following:

```
$ mvn spring-boot:run

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v2.0.0.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.222 seconds (JVM running for 6.514)
```

If you open a web browser to `localhost:8080`, you should see the following output:

```
Hello World!
```

To gracefully exit the application, press `ctrl-c`.

## 11.5 Creating an Executable Jar

We finish our example by creating a completely self-contained executable jar file that we could run in production. Executable jars (sometimes called “fat jars”) are archives containing your compiled classes along with all of the jar dependencies that your code needs to run.

**Executable jars and Java**

Java does not provide a standard way to load nested jar files (jar files that are themselves contained within a jar). This can be problematic if you are looking to distribute a self-contained application.

To solve this problem, many developers use “uber” jars. An uber jar packages all the classes from all the application’s dependencies into a single archive. The problem with this approach is that it becomes hard to see which libraries are in your application. It can also be problematic if the same filename is used (but with different content) in multiple jars.

Spring Boot takes a [different approach](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#executable-jar) and lets you actually nest jars directly.

To create an executable jar, we need to add the `spring-boot-maven-plugin` to our `pom.xml`. To do so, insert the following lines just below the `dependencies`section:

```Xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

| ![[Note]](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/images/note.png) |
| ------------------------------------------------------------ |
| The `spring-boot-starter-parent` POM includes `<executions>` configuration to bind the `repackage` goal. If you do not use the parent POM, you need to declare this configuration yourself. See the [plugin documentation](https://docs.spring.io/spring-boot/docs/2.0.0.RELEASE/maven-plugin/usage.html) for details. |

Save your `pom.xml` and run `mvn package` from the command line, as follows:

```
$ mvn package

[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building myproject 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] .... ..
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myproject ---
[INFO] Building jar: /Users/developer/example/spring-boot-example/target/myproject-0.0.1-SNAPSHOT.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:2.0.0.RELEASE:repackage (default) @ myproject ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```

If you look in the `target` directory, you should see `myproject-0.0.1-SNAPSHOT.jar`. The file should be around 10 MB in size. If you want to peek inside, you can use `jar tvf`, as follows:

```
$ jar tvf target/myproject-0.0.1-SNAPSHOT.jar
```

You should also see a much smaller file named `myproject-0.0.1-SNAPSHOT.jar.original` in the `target` directory. This is the original jar file that Maven created before it was repackaged by Spring Boot.

To run that application, use the `java -jar` command, as follows:

```
$ java -jar target/myproject-0.0.1-SNAPSHOT.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v2.0.0.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.536 seconds (JVM running for 2.864)
```

As before, to exit the application, press `ctrl-c`.

## 12. What to Read Next

Hopefully, this section provided some of the Spring Boot basics and got you on your way to writing your own applications. If you are a task-oriented type of developer, you might want to jump over to [spring.io](https://spring.io/) and check out some of the [getting started](https://spring.io/guides/) guides that solve specific “How do I do that with Spring?” problems. We also have Spring Boot-specific “[How-to](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto)” reference documentation.

The [Spring Boot repository](https://github.com/spring-projects/spring-boot) also has a [bunch of samples](https://github.com/spring-projects/spring-boot/tree/v2.0.0.RELEASE/spring-boot-samples) you can run. The samples are independent of the rest of the code (that is, you do not need to build the rest to run or use the samples).

Otherwise, the next logical step is to read *Part III, “Using Spring Boot”*. If you are really impatient, you could also jump ahead and read about *Spring Boot features*.

## 14. Structuring Your Code

Spring Boot does not require any specific code layout to work. However, there are some best practices that help.

## 14.1 Using the “default” Package

When a class does not include a `package` declaration, it is considered to be in the “default package”. The use of the “default package” is generally discouraged and should be avoided. It can cause particular problems for Spring Boot applications that use the `@ComponentScan`, `@EntityScan`, or `@SpringBootApplication`annotations, since every class from every jar is read.

| ![[Tip]](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/images/tip.png) |
| ------------------------------------------------------------ |
| We recommend that you follow Java’s recommended package naming conventions and use a reversed domain name (for example, `com.example.project`). |

## 14.2 Locating the Main Application Class

We generally recommend that you locate your main application class in a root package above other classes. **The `@EnableAutoConfiguration` annotation is often placed on your main class, and it implicitly defines a base “search package” for certain items**. For example, if you are writing a JPA application, the package of the`@EnableAutoConfiguration` annotated class is used to search for `@Entity` items.

Using a root package also lets the `@ComponentScan` annotation be used without needing to specify a `basePackage` attribute. You can also use the `@SpringBootApplication` annotation if your main class is in the root package.

The following listing shows a typical layout:

```
com
 +- example
     +- myapplication
         +- Application.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```

The `Application.java` file would declare the `main` method, along with the basic `@Configuration`, as follows:

```
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableAutoConfiguration
@ComponentScan
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

### 遇到问题

2018-04-13 日注入不了bean，原因是利用简单工厂，不能在返回的时候new 一个对象，需要提前把对象利用自动装配装配到工厂类里面，如下：

```
package com.colorv.bot.user.state.log.dao;


import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;

@Configuration
public class StateLoggerFactory {

    private UserStateDayLoggerDao userStateDayLoggerDao;
    private UserStateWeekLoggerDao userStateWeekLoggerDao;
    private UserStateMonthLoggerDao userStateMonthLoggerDao;

    @Autowired
    public void setUserStateDayLoggerDao(UserStateDayLoggerDao userStateDayLoggerDao) {
        this.userStateDayLoggerDao = userStateDayLoggerDao;
    }

    @Autowired
    public void setUserStateWeekLoggerDao(UserStateWeekLoggerDao userStateWeekLoggerDao) {
        this.userStateWeekLoggerDao = userStateWeekLoggerDao;
    }

    @Autowired
    public void setUserStateMonthLoggerDao(UserStateMonthLoggerDao userStateMonthLoggerDao) {
        this.userStateMonthLoggerDao = userStateMonthLoggerDao;
    }

    public UserStateLoggerIntf getStateLogger(String type) {
        switch (type) {
            case "d":
                return this.userStateDayLoggerDao; // 不能是 return new User..(), new 的话新的Dao 对象里面装配的东西肯定是没有装配上的
            case "w":
                return this.userStateWeekLoggerDao;
            case "m":
                return this.userStateMonthLoggerDao;
            default:
                return null;
        }

    }
}

```

### 启动Spring Boot

要把AppServer.java 文件放到至少一层目录下，不能直接在src/main/java 目录下。