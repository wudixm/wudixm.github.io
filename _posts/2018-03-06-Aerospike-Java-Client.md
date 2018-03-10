---
title: Aerospike Java 客户端
excerpt: |
  Aerospike Java 客户端
category: DataBase
feature_image: "https://picsum.photos/2560/600?image=872"
---
### Aerospike Java Client

```java
AerospikeClient client = null;
// Single Seed Node
client = new AerospikeClient("127.0.0.1", 3000);


// Multiple Seed Nodes
Host[] hosts = new Host[]{
        new Host("a.host", 3000),
        new Host("another.host", 3000),
        new Host("and.another.host", 3000)
};

// UserCounter Authentication
ClientPolicy policy = new ClientPolicy();
policy.user = "myuser";
policy.password = "mypass";


client = new AerospikeClient(new ClientPolicy(), hosts);

client.close()
```

### 查询：

```java
public UserCounter getUserByID(Integer id) {
    QueryPolicy policy = new QueryPolicy();

    Key key = new Key("coloru", "user_counter", "u." + id);

    Record record = client.get(policy, key);
    if (record == null) {
        return null;
    }
    Object videosPublishObj = record.getValue("videos_publish");
    long videos_publish = 0; // 如果在aerospike 中是数字，那么是按照long 值返回的
    if (videosPublishObj != null) {
        videos_publish = (Long) videosPublishObj;
    }

    long followers = 0;
    Object followersObj = record.getValue("followers");
    if (followersObj != null) {
        followers = (Long) followersObj;
    }

    UserCounter userCounter = new UserCounter();
    userCounter.setFollowers(followers);
    userCounter.setVideos_publish(videos_publish);
    userCounter.setUser_id(String.valueOf(id));

    return userCounter;
}
```

