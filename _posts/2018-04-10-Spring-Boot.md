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

{

    "2018-04-10": {
        "StateVO{activeStatus='NEW', interStatus='INTERACTION', worksStatus='NO_WORKS', watchLiveStatus='NO_WATCH_LIVE'}": 37,
        "StateVO{activeStatus='NEW', interStatus='NO_INTERACTION', worksStatus='NO_WORKS', watchLiveStatus='NO_WATCH_LIVE'}": 5790,
        "StateVO{activeStatus='NEW', interStatus='INTERACTION', worksStatus='WITH_WORKS', watchLiveStatus='NO_WATCH_LIVE'}": 17,
        "StateVO{activeStatus='NEW', interStatus='NO_INTERACTION', worksStatus='WITH_WORKS', watchLiveStatus='NO_WATCH_LIVE'}": 476
    }
    
        "2018-04-10": {
            "StateVO{activeStatus='NEW', interStatus='INTERACTION', worksStatus='NO_WORKS', watchLiveStatus='NO_WATCH_LIVE'}": 37,
            "StateVO{activeStatus='NEW', interStatus='NO_INTERACTION', worksStatus='NO_WORKS', watchLiveStatus='NO_WATCH_LIVE'}": 5744
        }
}