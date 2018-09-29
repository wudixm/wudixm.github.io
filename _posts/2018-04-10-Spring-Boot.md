---
title: Spring 配置方式
excerpt: |
  Spring 配置方式
category: Spring
feature_image: "https://picsum.photos/2560/600?image=872"
---
### Spring @RequestParam 与@RequestBody


```java
http://localhost:8000/userstatelog/?test=5
http://localhost:8000/userstatelog/?test=1

@RequestMapping(value = "/", method = RequestMethod.GET)
public UserCounter getUserByID(@RequestParam Map<String, Object> reqMap) throws Exception {//正确，从reqMap 中利用get 方法返回信息
		String type = (String) reqMap.get("type");
        System.out.println("type = " + type);   // 打印 “5”
		...
}

http://localhost:8000/userstatelog/?test=5

@RequestMapping(value = "/", method = RequestMethod.GET)
public UserCounter getUserByID(@RequestBody Map<String, Object> reqMap) throws Exception {// 报错，因为是GET 方法，则无请求体，reqMap 在这里又是利用@RequestBody 赋值，肯定无法找到这个对象
		String type = (String) reqMap.get("type");
        System.out.println("type = " + type);
		...
}
```

- 如果是RequestBody 一定要在**http 请求体**中加上内容，利用PostMan 或者其它Http 工具进行测试，所以应该是一定要POST 请求才可以(GET 无请求体)

# Spring的Java配置方式

Java配置是Spring4.x推荐的配置方式，可以完全替代xml配置。

## @Configuration和 @Bean

Spring的Java配置方式是通过@Configuration 和 @Bean这两个注解实现的：

1、@Configuration作用于类上，相当于一个xml配置文件；

2、@Bean作用于方法上，相当于xml配置中的<bean>；

## 79.1 Configure a Custom DataSource

To configure your own `DataSource`, define a `@Bean` of that type in your configuration. Spring Boot reuses your `DataSource` anywhere one is required, including database initialization. If you need to externalize some settings, you can bind your `DataSource` to the environment (see “[Section 24.7.1, “Third-party Configuration”](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html#boot-features-external-config-3rd-party-configuration)”).

The following example shows how to define a data source in a bean:

```
@Bean
@ConfigurationProperties(prefix="app.datasource")
public DataSource dataSource() {
	return new FancyDataSource();
}
```

The following example shows how to define a data source by setting properties:

```
app.datasource.url=jdbc:h2:mem:mydb
app.datasource.username=sa
app.datasource.pool-size=30
```

Assuming that your `FancyDataSource` has regular JavaBean properties for the URL, the username, and the pool size, these settings are bound automatically before the `DataSource` is made available to other components. The regular [database initialization](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-database-initialization.html#howto-initialize-a-database-using-spring-jdbc) also happens (so the relevant sub-set of `spring.datasource.*` can still be used with your custom configuration).

You can apply the same principle if you configure a custom JNDI `DataSource`, as shown in the following example:

```
@Bean(destroyMethod="")
@ConfigurationProperties(prefix="app.datasource")
public DataSource dataSource() throws Exception {
	JndiDataSourceLookup dataSourceLookup = new JndiDataSourceLookup();
	return dataSourceLookup.getDataSource("java:comp/env/jdbc/YourDS");
}
```

Spring Boot also provides a utility builder class, called `DataSourceBuilder`, that can be used to create one of the standard data sources (if it is on the classpath). The builder can detect the one to use based on what’s available on the classpath. It also auto-detects the driver based on the JDBC URL.

The following example shows how to create a data source by using a `DataSourceBuilder`:

```
@Bean
@ConfigurationProperties("app.datasource")
public DataSource dataSource() {
	return DataSourceBuilder.create().build();
}
```

To run an app with that `DataSource`, all you need is the connection information. Pool-specific settings can also be provided. Check the implementation that is going to be used at runtime for more details.

The following example shows how to define a JDBC data source by setting properties:

```
app.datasource.url=jdbc:mysql://localhost/test
app.datasource.username=dbuser
app.datasource.password=dbpass
app.datasource.pool-size=30
```

However, there is a catch. Because the actual type of the connection pool is not exposed, no keys are generated in the metadata for your custom `DataSource` and no completion is available in your IDE (because the `DataSource` interface exposes no properties). Also, if you happen to have Hikari on the classpath, this basic setup does not work, because Hikari has no `url` property (but does have a `jdbcUrl` property). In that case, you must rewrite your configuration as follows:

```
app.datasource.jdbc-url=jdbc:mysql://localhost/test
app.datasource.username=dbuser
app.datasource.password=dbpass
app.datasource.maximum-pool-size=30
```

You can fix that by forcing the connection pool to use and return a dedicated implementation rather than `DataSource`. You cannot change the implementation at runtime, but the list of options will be explicit.

The following example shows how create a `HikariDataSource` with `DataSourceBuilder`:

```
@Bean
@ConfigurationProperties("app.datasource")
public HikariDataSource dataSource() {
	return DataSourceBuilder.create().type(HikariDataSource.class).build();
}
```

You can even go further by leveraging what `DataSourceProperties` does for you — that is, by providing a default embedded database with a sensible username and password if no URL is provided. You can easily initialize a `DataSourceBuilder` from the state of any `DataSourceProperties` object, so you could also inject the DataSource that Spring Boot creates automatically. However, that would split your configuration into two namespaces: `url`, `username`, `password`, `type`, and `driver` on `spring.datasource` and the rest on your custom namespace (`app.datasource`). To avoid that, you can redefine a custom`DataSourceProperties` on your custom namespace, as shown in the following example:

```
@Bean
@Primary
@ConfigurationProperties("app.datasource")
public DataSourceProperties dataSourceProperties() {
	return new DataSourceProperties();
}

@Bean
@ConfigurationProperties("app.datasource")
public HikariDataSource dataSource(DataSourceProperties properties) {
	return properties.initializeDataSourceBuilder().type(HikariDataSource.class)
			.build();
}
```

This setup puts you *in sync* with what Spring Boot does for you by default, except that a dedicated connection pool is chosen (in code) and its settings are exposed in the same namespace. Because `DataSourceProperties` is taking care of the `url`/`jdbcUrl` translation for you, you can configure it as follows:

```
app.datasource.url=jdbc:mysql://localhost/test
app.datasource.username=dbuser
app.datasource.password=dbpass
app.datasource.maximum-pool-size=30
```

| ![[Note]](https://docs.spring.io/spring-boot/docs/current/reference/html/images/note.png) |
| ------------------------------------------------------------ |
| Because your custom configuration chooses to go with Hikari, `app.datasource.type` has no effect. In practice, the builder is initialized with whatever value you might set there and then overridden by the call to `.type()`. |

See “[Section 29.1, “Configure a DataSource”](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-sql.html#boot-features-configure-datasource)” in the “Spring Boot features” section and the [`DataSourceAutoConfiguration`](https://github.com/spring-projects/spring-boot/tree/v2.0.2.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/jdbc/DataSourceAutoConfiguration.java) class for more details.

## Spring @ControllerAdvice Exception Handle

[原文](http://www.baeldung.com/exception-handling-for-rest-with-spring)

### Solution 1 – The Controller level @ExceptionHandler

The first solution works at the *@Controller* level – we will define a method to handle exceptions, and annotate that with *@ExceptionHandler*:

```java
public class FooController{
     
    //...
    @ExceptionHandler({ CustomException1.class, CustomException2.class })
    public void handleException() {
        //
    }
}
```

This approach has **a major drawback** – the *@ExceptionHandler* annotated method is **only active for that particular Controller**, not globally for the entire application. Of course adding this to every controller makes it not well suited for a general exception handling mechanism.

### New Solution 3 – The New @ControllerAdvice (Spring 3.2 And Above)

**Spring 3.2** brings support for a global *@ExceptionHandler *with the new *@ControllerAdvice* annotation. This enables a mechanism that breaks away from the older MVC model and makes use of *ResponseEntity*along with the type safety and flexibility of *@ExceptionHandler*:

```java
@ControllerAdvice
public class RestResponseEntityExceptionHandler extends ResponseEntityExceptionHandler {
 
    @ExceptionHandler(value = { IllegalArgumentException.class, IllegalStateException.class })
    protected ResponseEntity<Object> handleConflict(RuntimeException ex, WebRequest request) {
        String bodyOfResponse = "This should be application specific";
        return handleExceptionInternal(ex, bodyOfResponse, 
          new HttpHeaders(), HttpStatus.CONFLICT, request);
    }
}
```

The new annotation allows the multiple scattered *@ExceptionHandler* from before to be consolidated into a **single, global error handling component**.

The actual mechanism is extremely simple but also very flexible:

- it allows full control over the body of the response as well as the status code
- it allows mapping of several exceptions to the same method, to be handled together
- it makes good use of the newer RESTful *ResposeEntity* response**

One thing to keep in mind here is to **match the exceptions declared with @ExceptionHandler with the exception used as the argument of the method**. If these don’t match, the compiler will not complain – no reason it should, and Spring will not complain either.

However, when the exception is actually thrown at runtime, **the exception resolving mechanism will fail with**:

```
java.lang.IllegalStateException: No suitable resolver for argument [0] [type=...]
HandlerMethod details: ...
```

### GET, POST

```
package com.example.controller;
 
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
 
import com.example.bean.RequestLoginBean;
import com.example.response.BaseResponse;
import com.google.gson.Gson;
 
@RestController
@RequestMapping(value = "/index")
public class Login {
 
	/**
	 * index home
	 * 
	 * @return
	 */
	@RequestMapping(value = "/home")
	public String home() {
		return "index home";
	}
	
	/**
	 * 得到1个参数
	 * 
	 * @param name
	 *            用户名
	 * @return 返回结果
	 */
	@GetMapping(value = "/{name}")
	public String index(@PathVariable String name) {
		return "oh you are " + name + "<br> nice to meet you";// \n不起作用了,那就直接用html中的标签吧
	}
 
	/**
	 * 简单post请求
	 * 
	 * @param name
	 * @param pwd
	 * @return
	 */
	@RequestMapping(value = "/testpost", method = RequestMethod.POST)
	public String testpost() {
		System.out.println("hello  test post");
		return "ok";
	}
 
	/**
	 * 同时得到两个参数
	 * 
	 * @param name
	 *            用户名
	 * @param pwd
	 *            密码
	 * @return 返回结果
	 */
	@GetMapping(value = "/login/{name}&{pwd}")
	public String login(@PathVariable String name, @PathVariable String pwd) {
		if (name.equals("admin") && pwd.equals("admin")) {
			return "hello welcome admin";
		} else {
			return "oh sorry user name or password is wrong";
		}
	}
 
	/**
	 * 通过get请求去登陆
	 * 
	 * @param name
	 * @param pwd
	 * @return
	 */
	@RequestMapping(value = "/loginbyget", method = RequestMethod.GET)
	public String loginByGet(@RequestParam(value = "name", required = true) String name,
			@RequestParam(value = "pwd", required = true) String pwd) {
		return login4Return(name, pwd);
	}
 
	/**
	 * 通过post请求去登陆
	 * 
	 * @param name
	 * @param pwd
	 * @return
	 */
	@RequestMapping(value = "/loginbypost", method = RequestMethod.POST)
	public String loginByPost(@RequestParam(value = "name", required = true) String name,
			@RequestParam(value = "pwd", required = true) String pwd) {
		System.out.println("hello post");
		return login4Return(name, pwd);
	}
 
	/**
	 * 参数为一个bean对象.spring会自动为我们关联映射
	 * @param loginBean
	 * @return
	 */
	@RequestMapping(value = "/loginbypost1", method = { RequestMethod.POST, RequestMethod.GET })
	public String loginByPost1(RequestLoginBean loginBean) {
		if (null != loginBean) {
			return login4Return(loginBean.getName(), loginBean.getPwd());
		} else {
			return "error";
		}
	}
	
	/**
	 * 请求内容是一个json串,spring会自动把他和我们的参数bean对应起来,不过要加@RequestBody注解
	 * 
	 * @param name
	 * @param pwd
	 * @return
	 */
	@RequestMapping(value = "/loginbypost2", method = { RequestMethod.POST, RequestMethod.GET })
	public String loginByPost2(@RequestBody RequestLoginBean loginBean) {
		if (null != loginBean) {
			return login4Return(loginBean.getName(), loginBean.getPwd());
		} else {
			return "error";
		}
	}
 
	
 
 
	/**
	 * 对登录做出响应处理的方法
	 * 
	 * @param name
	 *            用户名
	 * @param pwd
	 *            密码
	 * @return 返回处理结果
	 */
	private String login4Return(String name, String pwd) {
		String result;
		BaseResponse response = new BaseResponse();
		if (name.equals("admin") && pwd.equals("admin")) {
			result = "hello welcome admin";
			response.setState(true);
		} else {
			result = "oh sorry user name or password is wrong";
			response.setState(false);
		}
		System.out.println("收到请求,请求结果:" + result);
		return new Gson().toJson(response);
	}
}

```

[原文](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/boot-redis.html)

# Spring Session - Spring Boot

Rob Winch, Vedran PavićVersion 2.0.4.RELEASE

Table of Contents

- [1. Updating Dependencies](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/boot-redis.html#updating-dependencies)
- [2. Spring Boot Configuration](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/boot-redis.html#boot-spring-configuration)
- [3. Configuring the Redis Connection](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/boot-redis.html#boot-redis-configuration)
- [4. Servlet Container Initialization](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/boot-redis.html#boot-servlet-configuration)
- \5. Boot Sample Application
  - [5.1. Running the Boot Sample Application](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/boot-redis.html#boot-running)
  - [5.2. Exploring the security Sample Application](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/boot-redis.html#boot-explore)
  - [5.3. How does it work?](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/boot-redis.html#boot-how)

This guide describes how to use Spring Session to transparently leverage Redis to back a web application’s `HttpSession` when using Spring Boot.

| **   | The completed guide can be found in the [boot sample application](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/boot-redis.html#boot-sample). |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

## 1. Updating Dependencies

Before you use Spring Session, you must ensure to update your dependencies. We assume you are working with a working Spring Boot web application. If you are using Maven, ensure to add the following dependencies:

pom.xml

```
<dependencies>
	<!-- ... -->

	<dependency>
		<groupId>org.springframework.session</groupId>
		<artifactId>spring-session-data-redis</artifactId>
	</dependency>
</dependencies>
```

Spring Boot provides dependency management for Spring Session modules, so there’s no need to explicitly declare dependency version.

## 2. Spring Boot Configuration

After adding the required dependencies, we can create our Spring Boot configuration. Thanks to first-class auto configuration support, setting up Spring Session backed by Redis is as simple as adding a single configuration property to your `application.properties`:

src/main/resources/application.properties

```
spring.session.store-type=redis # Session store type.
```

Under the hood, Spring Boot will apply configuration that is equivalent to manually adding `@EnableRedisHttpSession`annotation. This creates a Spring Bean with the name of `springSessionRepositoryFilter` that implements Filter. The filter is what is in charge of replacing the `HttpSession` implementation to be backed by Spring Session.

Further customization is possible using `application.properties`:

src/main/resources/application.properties

```
server.servlet.session.timeout= # Session timeout. If a duration suffix is not specified, seconds will be used.
spring.session.redis.flush-mode=on-save # Sessions flush mode.
spring.session.redis.namespace=spring:session # Namespace for keys used to store sessions.
```

For more information, refer to [Spring Session](https://docs.spring.io/spring-boot/docs/2.0.2.RELEASE/reference/htmlsingle/#boot-features-session) portion of the Spring Boot documentation.

## 3. Configuring the Redis Connection

Spring Boot automatically creates a `RedisConnectionFactory` that connects Spring Session to a Redis Server on localhost on port 6379 (default port). In a production environment you need to ensure to update your configuration to point to your Redis server. For example, you can include the following in your **application.properties**

src/main/resources/application.properties

```
spring.redis.host=localhost # Redis server host.
spring.redis.password= # Login password of the redis server.
spring.redis.port=6379 # Redis server port.
```

For more information, refer to [Connecting to Redis](https://docs.spring.io/spring-boot/docs/2.0.2.RELEASE/reference/htmlsingle/#boot-features-connecting-to-redis) portion of the Spring Boot documentation.

## 4. Servlet Container Initialization

Our [Spring Boot Configuration](https://docs.spring.io/spring-session/docs/current/reference/html5/guides/boot-redis.html#boot-spring-configuration) created a Spring Bean named `springSessionRepositoryFilter` that implements `Filter`. The `springSessionRepositoryFilter` bean is responsible for replacing the `HttpSession` with a custom implementation that is backed by Spring Session.

In order for our `Filter` to do its magic, Spring needs to load our `Config` class. Last we need to ensure that our Servlet Container (i.e. Tomcat) uses our `springSessionRepositoryFilter` for every request. Fortunately, Spring Boot takes care of both of these steps for us.

## 5. Boot Sample Application

The Boot Sample Application demonstrates how to use Spring Session to transparently leverage Redis to back a web application’s `HttpSession` when using Spring Boot.

### 5.1. Running the Boot Sample Application

You can run the sample by obtaining the [source code](https://github.com/spring-projects/spring-session/archive/2.0.4.RELEASE.zip) and invoking the following command:

| **   | For the sample to work, you must [install Redis 2.8+](https://redis.io/download) on localhost and run it with the default port (6379). Alternatively, you can update the `RedisConnectionFactory` to point to a Redis server. Another option is to use [Docker](https://www.docker.com/) to run Redis on localhost. See [Docker Redis repository](https://hub.docker.com/_/redis/) for detailed instructions. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

```
$ ./gradlew :spring-session-sample-boot-redis:bootRun
```

You should now be able to access the application at <http://localhost:8080/>

### 5.2. Exploring the security Sample Application

Try using the application. Enter the following to log in:

- **Username** *user*
- **Password** *password*

Now click the **Login** button. You should now see a message indicating your are logged in with the user entered previously. The user’s information is stored in Redis rather than Tomcat’s `HttpSession` implementation.

### 5.3. How does it work?

Instead of using Tomcat’s `HttpSession`, we are actually persisting the values in Redis. Spring Session replaces the `HttpSession` with an implementation that is backed by Redis. When Spring Security’s `SecurityContextPersistenceFilter` saves the `SecurityContext` to the `HttpSession` it is then persisted into Redis.

When a new `HttpSession` is created, Spring Session creates a cookie named SESSION in your browser that contains the id of your session. Go ahead and view the cookies (click for help with [Chrome](https://developers.google.com/web/tools/chrome-devtools/manage-data/cookies) or [Firefox](https://developer.mozilla.org/en-US/docs/Tools/Storage_Inspector)).

If you like, you can easily remove the session using redis-cli. For example, on a Linux based system you can type:

```
$ redis-cli keys '*' | xargs redis-cli del
```

| **   | The Redis documentation has instructions for [installing redis-cli](https://redis.io/topics/quickstart). |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

Alternatively, you can also delete the explicit key. Enter the following into your terminal ensuring to replace `7e8383a4-082c-4ffe-a4bc-c40fd3363c5e` with the value of your SESSION cookie:

```
$ redis-cli del spring:session:sessions:7e8383a4-082c-4ffe-a4bc-c40fd3363c5e
```

Now visit the application at <http://localhost:8080/> and observe that we are no longer authenticated.

Version 2.0.4.RELEASE
Last updated 2018-04-05 09:49:06 +00:00
