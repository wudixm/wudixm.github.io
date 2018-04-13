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

