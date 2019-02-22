##  spring-actuator

> spring-boot-actuator模块提供了一个监控和管理生产环境的模块，可以使用http、jmx、ssh、telnet等来管理和监控应用。审计（Auditing）、   健康（health）、数据采集（metrics gathering）会自动加入到应用里面。

### 添加依赖

```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

可以在你的`application.yml`配置文件配置：

```
##运行状态 actuator监控
endpoints:
  enabled: true
  info:
    sensitive: false
  health:
    sensitive: false
management:
  ##服务路径
  context-path: /
  ##服务端口
  port: 8081
```

如果你使用`application.properties`，可以做类似的配置：

```
endpoints.enabled=true
endpoints.info.sensitive=false
endpoints.health.sensitive=false
management.context-path=/
management.port=8081
```

启动后可以看到以下日志：

```
2017-03-15 15:50:58.775  INFO 9544 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Detected ResponseBodyAdvice bean in org.springframework.boot.actuate.autoconfigure.EndpointWebMvcHypermediaManagementContextConfiguration$ActuatorEndpointLinksAdvice
2017-03-15 15:50:58.956  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/dump || /dump.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
2017-03-15 15:50:58.957  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/configprops || /configprops.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
2017-03-15 15:50:58.958  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/metrics/{name:.*}],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.MetricsMvcEndpoint.value(java.lang.String)
2017-03-15 15:50:58.958  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/metrics || /metrics.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
2017-03-15 15:50:58.959  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/health || /health.json],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.HealthMvcEndpoint.invoke(java.security.Principal)
2017-03-15 15:50:58.960  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/autoconfig || /autoconfig.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
2017-03-15 15:50:58.960  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/mappings || /mappings.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
2017-03-15 15:50:58.961  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/beans || /beans.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
2017-03-15 15:50:58.963  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/heapdump || /heapdump.json],methods=[GET],produces=[application/octet-stream]}" onto public void org.springframework.boot.actuate.endpoint.mvc.HeapdumpMvcEndpoint.invoke(boolean,javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse) throws java.io.IOException,javax.servlet.ServletException
2017-03-15 15:50:58.964  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/actuator || /actuator.json],produces=[application/json]}" onto public org.springframework.hateoas.ResourceSupport org.springframework.boot.actuate.endpoint.mvc.HalJsonMvcEndpoint.links()
2017-03-15 15:50:58.965  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/info || /info.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
2017-03-15 15:50:58.973  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/env/{name:.*}],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EnvironmentMvcEndpoint.value(java.lang.String)
2017-03-15 15:50:58.973  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/env || /env.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
2017-03-15 15:50:58.974  INFO 9544 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/trace || /trace.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
```

## 项目中使用

```shell
curl -H "Content-Type: application/json" -X POST --data "{\"configuredLevel\": \"DEBUG\"}" http://localhost:8000/actuator/loggers/com.colorv
```

