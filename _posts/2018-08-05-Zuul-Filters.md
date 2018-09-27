# How it Works

matthew hawthorne edited this page on 5 Jun 2013 · [5 revisions](https://github.com/Netflix/zuul/wiki/How-it-Works/_history)

[原文](https://github.com/Netflix/zuul/wiki/How-it-Works)

###  Pages 16

- [Home](https://github.com/Netflix/zuul/wiki/Home)
- Zuul 2.x
  - [Getting Started 2.0](https://github.com/Netflix/zuul/wiki/Getting-Started-2.0)
  - [How It Works 2.0](https://github.com/Netflix/zuul/wiki/How-It-Works-2.0)
  - [Server Configuration](https://github.com/Netflix/zuul/wiki/Server-Configuration)
  - [Filters](https://github.com/Netflix/zuul/wiki/Filters)
  - Core Features
    - [Service Discovery](https://github.com/Netflix/zuul/wiki/Core-Features#service-discovery)
    - [Load Balancing](https://github.com/Netflix/zuul/wiki/Core-Features#load-balancing)
    - [Connection Pooling](https://github.com/Netflix/zuul/wiki/Core-Features#connection-pooling)
    - [Status Categories](https://github.com/Netflix/zuul/wiki/Core-Features#status-categories)
    - [Retries](https://github.com/Netflix/zuul/wiki/Core-Features#retries)
    - [Request Passport](https://github.com/Netflix/zuul/wiki/Core-Features#request-passport)
    - [Request Attempts](https://github.com/Netflix/zuul/wiki/Core-Features#request-attempts)
    - [Origin Concurrency Protection](https://github.com/Netflix/zuul/wiki/Core-Features#origin-concurrency-protection)
    - [HTTP/2](https://github.com/Netflix/zuul/wiki/Core-Features#http2)
    - [Mutual TLS](https://github.com/Netflix/zuul/wiki/Core-Features#mutual-tls)
    - [Proxy Protocol](https://github.com/Netflix/zuul/wiki/Core-Features#proxy-protocol)
    - [GZip](https://github.com/Netflix/zuul/wiki/Core-Features#gzip)
  - [Push Messaging](https://github.com/Netflix/zuul/wiki/Push-Messaging)
  - [Benchmarking](https://github.com/Netflix/zuul/wiki/Benchmarking)
  - [Coming Soon](https://github.com/Netflix/zuul/wiki/Coming-Soon)
- Zuul 1.x
  - [Getting Started](https://github.com/Netflix/zuul/wiki/Getting-Started)
  - [How It Works](https://github.com/Netflix/zuul/wiki/How-it-Works)
  - [How We Use Zuul At Netflix](https://github.com/Netflix/zuul/wiki/How-We-Use-Zuul-At-Netflix)
  - How To Use
    - [Simple Example App: zuul-simple-webapp](https://github.com/Netflix/zuul/wiki/zuul-simple-webapp)
    - [Comprehensive Example App: zuul-netflix-webapp](https://github.com/Netflix/zuul/wiki/zuul-netflix-webapp)
    - [Writing Filters](https://github.com/Netflix/zuul/wiki/Writing-Filters)

##### Clone this wiki locally

 Clone in Desktop

## Filter Overview

At the center of Zuul is a series of Filters that are capable of performing a range of actions during the routing of HTTP requests and responses.

The following are the key characteristics of a Zuul Filter:

- **Type**: most often defines the stage during the routing flow when the Filter will be applied (although it can be any custom string)
- **Execution Order**: applied within the Type, defines the order of execution across multiple Filters
- **Criteria**: the conditions required in order for the Filter to be executed
- **Action**: the action to be executed if the Criteria is met

Zuul provides a framework to dynamically read, compile, and run these Filters. Filters do not communicate with each other directly - instead they share state through a RequestContext which is unique to each request.

Filters are currently written in Groovy, although Zuul supports any JVM-based language. The source code for each Filter is written to a specified set of directories on the Zuul server that are periodically polled for changes. Updated filters are read from disk, dynamically compiled into the running server, and are invoked by Zuul for each subsequent request.

## Filter Types

There are several standard Filter types that correspond to the typical lifecycle of a request:

- **PRE** Filters execute before routing to the origin. Examples include request authentication, choosing origin servers, and logging debug info.
- **ROUTING** Filters handle routing the request to an origin. This is where the origin HTTP request is built and sent using Apache HttpClient or Netflix Ribbon.
- **POST** Filters execute after the request has been routed to the origin. Examples include adding standard HTTP headers to the response, gathering statistics and metrics, and streaming the response from the origin to the client.
- **ERROR** Filters execute when an error occurs during one of the other phases.

Alongside the default Filter flow, Zuul allows us to create custom filter types and execute them explicitly. For example, we have a custom STATIC type that generates a response within Zuul instead of forwarding the request to an origin. We have a few use cases for this, one of which is internal endpoints that contain debug data about a particular Zuul instance.

## Zuul Request Lifecycle

![zuul-request-lifecycle.png](https://camo.githubusercontent.com/4eb7754152028cdebd5c09d1c6f5acc7683f0094/687474703a2f2f6e6574666c69782e6769746875622e696f2f7a75756c2f696d616765732f7a75756c2d726571756573742d6c6966656379636c652e706e67)

### Debug Filter

set `zuul.include-debug-header=true`, and add request parameter`debug=true`when you want to debug a request, like `/api/example?debug=true`

```
server:
  port: 8762
...
zuul:
  routes:
    rest-applica: /myusers/**
  include-debug-header: true # 如果是false，那么下面的x-zuul-debug-encoding 不显示

http://192.168.203.11:8762/myusers?b=2&debug=true
content-type →text/plain;charset=UTF-8
date →Mon, 06 Aug 2018 04:39:17 GMT
transfer-encoding →chunked
x-zuul-debug-header →[[[Filter pre 5 PreDecorationFilter]]][[[Filter {PreDecorationFilter TYPE:pre ORDER:5} Execution time = 4ms]]][[[{PreDecorationFilter} added retryable=false]]][[[{PreDecorationFilter} added ignoredHeaders=[authorization, set-cookie, cookie]]]][[[{PreDecorationFilter} added originResponseHeaders=[com.netflix.util.Pair@8054024e]]]][[[{PreDecorationFilter} added zuulRequestHeaders={x-forwarded-host=192.168.203.11:8762, x-forwarded-proto=http, x-forwarded-prefix=/myusers, x-forwarded-port=8762, x-forwarded-for=192.168.203.11}]]][[[{PreDecorationFilter} added requestURI=]]][[[{PreDecorationFilter} added proxy=myusers]]][[[{PreDecorationFilter} changed executedFilters=ServletDetectionFilter[SUCCESS][0ms], Servlet30WrapperFilter[SUCCESS][0ms], DebugFilter[SUCCESS][0ms], PreDecorationFilter[SUCCESS][4ms]]]][[[{PreDecorationFilter} added serviceId=rest-applica]]][[[Invoking {route} type filters]]][[[Filter route 10 RibbonRoutingFilter]]][[[Filter {RibbonRoutingFilter TYPE:route ORDER:10} Execution time = 17ms]]][[[{RibbonRoutingFilter} changed originResponseHeaders=[com.netflix.util.Pair@8054024e, com.netflix.util.Pair@13ea01aa, com.netflix.util.Pair@b79e9b40, com.netflix.util.Pair@21a9bdd5]]]][[[{RibbonRoutingFilter} added responseDataStream=org.apache.http.conn.EofSensorInputStream@467f1d88]]][[[{RibbonRoutingFilter} added zuulResponseHeaders=[com.netflix.util.Pair@13ea01aa, com.netflix.util.Pair@21a9bdd5]]]][[[{RibbonRoutingFilter} added responseStatusCode=200]]][[[{RibbonRoutingFilter} added responseGZipped=false]]][[[{RibbonRoutingFilter} added ribbonResponse=org.springframework.cloud.netflix.ribbon.apache.RibbonApacheHttpResponse@19654b1a]]][[[{RibbonRoutingFilter} added originContentLength=21]]][[[{RibbonRoutingFilter} changed executedFilters=ServletDetectionFilter[SUCCESS][0ms], Servlet30WrapperFilter[SUCCESS][0ms], DebugFilter[SUCCESS][0ms], PreDecorationFilter[SUCCESS][4ms], RibbonRoutingFilter[SUCCESS][17ms]]]][[[{RibbonRoutingFilter} added zuulResponse=org.springframework.cloud.netflix.ribbon.RibbonHttpResponse@309951d4]]][[[Filter route 100 SimpleHostRoutingFilter]]][[[Filter route 500 SendForwardFilter]]][[[Invoking {post} type filters]]][[[Filter post 1000 SendResponseFilter]]]

```

Application.yml

```
zuul:
  routes:
    rest-applica: /myusers/**
  include-debug-header: true
  debug:
    request: true
```

zuul.debug.request = true 好像没什么作用，还是不打印日志