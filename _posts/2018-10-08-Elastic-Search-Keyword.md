[fielddata](https://www.elastic.co/guide/en/elasticsearch/reference/current/fielddata.html)

### 问题

如果一个字段在导入elastic search 中的时候，类型是text，那么在搜索的时候，搜索这个字段的时候，不能对其直接操作，必须在字段名字后面加`.keyword`。

```
query = {
        "bool": {
            "must": [
                {"term": {"lyric_state": "1"}},
                {"exists": {"field": "krc_path"}},
                {"multi_match": {"query": w, "fields": ["name", "artist"]}}
            ],
            "must_not": [{"term": {"krc_path": ""}}],
            "should": [],
        }
    }

```

要改成：

```
query = {
        "bool": {
            "must": [
                {"term": {"lyric_state": "1"}},
                {"exists": {"field": "krc_path.keyword"}},
                {"multi_match": {"query": w, "fields": ["name", "artist"]}}
            ],
            "must_not": [{"term": {"krc_path.keyword": ""}}],
            "should": [],
        }
    }

```

### 匹配嵌套的值

如果保存的是对象，想匹配对象的某个属性，如存储的doc 是

```
doc = {
            'id': model.id,
            ...
            'creator': {
                'user_id': model.user_id,
                'user_name': user.name,
                'user_age_zone': user.age_zone,
                'udid': user.udid,
                'os': 'iOS' if len(user.udid) == 32 else 'android',
                'platform': user.platform,
            },
            ...
        }
```

需要查询creator 里的user_id 匹配的，则查询时用 `creator.user_id` 就可以了，之间用点号连接：

```
{
  "query": {
    "bool": {
      "must": {
        "match": {
          "creator.user_id": 3764284
        }
      }
    }
  },
  "size": 2
}
```

### minimum_should_search 

```
{"query": {
  "bool": {
  "should": [
      {"match": {
      "a": "1"
    　},
　　　 {"match": {
　　　　"b": "2"
　　　　}}
　　]
}}}
```

那如果我们将上述条件改为：满足a=1或b=2，并且c=3，也许很多朋友会犯跟我一样的错误，即在上述代码中增加一个must：c=3，即：

```
{"query": {
  "bool": {
　　"must": [
  　　{"match": {
    　　"c": "3"
　　}}
　　],
  "should": [
      {"match": {
      "a": "1"
    　},
　　　 {"match": {
　　　　"b": "2"
　　　　}}
　　]
}}}
```

乍一看，没什么问题，即满足must中的条件，并满足should中的一个条件，然而，这样一运行，得到的结果好像只有c=3，而少了a和b的过滤；怎么回事呢？

**其实shoule在与must或者filter同级时，默认是不需要满足should中的任何条件的，此时我们可以加上minimum_should_match 参数，来达到我们的目的**
，即上述代码改为：

```
{"query": {
  "bool": {
　　"must": [
  　　{"match": {
    　　"c": "3"
　　}}
　　],
  "should": [
      {"match": {
      "a": "1"
    　},
　　　 {"match": {
　　　　"b": "2"
　　　　}}
　　],
    "minimum_should_match":1
}}}
```

上述代码表示，必须满足must中的所有条件，并且至少满足should中的一个条件，这样，就得到了预期的结果。

当然，我们也可以使用另一种方式实现，即将should放入到must中的一个bool条件中，即用分层的方式让should和must不同时出现，实现如下：

```
{"query": {
    "bool": {
　　    "must": [
  　　  {
            "match": { "c": "3" },
            "bool": {
                "should": [
  　　          {
                  "match": { "a": "1"  },
                  "match": { "b": "2"  }
                }
        }
　　]
}}}
```

### 常用命令

[原文](http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html)

```sh
# node说明信息
/u/l/C/e/7/l/bin  curl localhost:9200
{
  "name" : "wtfmacdeMacBook-Pro.local",
  "cluster_name" : "elasticsearch_brew",
  "cluster_uuid" : "IBxU5vS_RxCu8sr3zQZV6A",
  "version" : {
    "number" : "7.10.2-SNAPSHOT",
    "build_flavor" : "oss",
    "build_type" : "tar",
    "build_hash" : "unknown",
    "build_date" : "2021-01-16T01:34:41.142971Z",
    "build_snapshot" : true,
    "lucene_version" : "8.7.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```
下面的命令可以查看当前节点的所有 Index。

```sh
/u/l/v/l/elasticsearch  curl -X GET 'http://localhost:9200/_cat/indices?v'
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   test  dokdUOTWTX6d1I1imxgqQg   1   1          0            0       208b           208b
```

下面的命令可以列出每个 Index 所包含的 Type。
```sh
/u/l/v/l/elasticsearch  curl 'localhost:9200/_mapping?pretty=true'
{
  "test" : {
    "mappings" : { }
  }
}
```

新建 Index，可以直接向 Elastic 服务器发出 PUT 请求。下面的例子是新建一个名叫weather的 Index。

```sh
/u/l/C/e/7/l/bin  curl -X PUT 'localhost:9200/test'
{"acknowledged":true,"shards_acknowledged":true,"index":"test"}⏎
```

服务器返回一个 JSON 对象，里面的acknowledged字段表示操作成功。


{
  "acknowledged":true,
  "shards_acknowledged":true
}
然后，我们发出 DELETE 请求，删除这个 Index。
```sh
/u/l/C/e/7/l/bin  curl -X DELETE 'localhost:9200/test'
{"acknowledged":true}⏎
```

### 中文分词

用[ik](https://github.com/medcl/elasticsearch-analysis-ik/)，或者[smartcn](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-smartcn.html)

### Network.host: 0.0.0.0 is not working in elasticsearch 7.3.0

[原文](https://discuss.elastic.co/t/network-host-0-0-0-0-is-not-working-in-elasticsearch-7-3-0/195697)

如果配置了network.host: 0.0.0.0 在elasticsearch.yml 配置文件中，而且还出现curl 不到9200端口，则需要加cluster.initial_master_nodes: node-1
I am using two elasticsearch in two separate servers. one is 6.8.0 and other is 7.3.0.

I am planning to use metricbeat on several servers and visualize in elasticsearch server by kibana.

The problem is: when I configure elasticsearch to accept request from all servers in 6.8.0, I have simply added network.host: 0.0.0.0 in the elasticsearch.yml file and its working fine.

But, when I do the same in 7.3.0, it stops automatically after some seconds, I cannot curl http://127.0.0.1:9200 17.

I have checked the firewall, there is no problem there.

Can you please, help.

Hi, got it. Added this line to the elasticsearch.yml file and problem is solved: cluster.initial_master_nodes: node-1

### 搭建es 集群
[原文](https://www.cnblogs.com/chenyanbin/p/13493920.html)

Node节点
一个ElasticSearch实例即一个Node，一台机器可以有多个实例，正常使用下每个实例应该会部署在不同机器上。ElasticSearch的配置文件中可以通过node.master、node.data来设置节点类型。

　　node.master：表示节点是否具有称为主节点的资格

　　　　true代表的是有资格竞选主节点

　　　　false代表的是没有资格竞选主节点

　　node.data：表示节点是否存储数据


Node节点组合
主节点+数据节点(master+data)，节点即有称为主节点的资格，又存储数据

node.master: true
node.data: true

数据节点(data)，节点没有成为主节点的资格，不参与选举，只会存储数据

node.master: false
node.data: true

客户端节点(client)，不会成为主节点，也不会存储数据，主要是针对海量请求的时候，可以进行负载均衡
node.master: false
node.data: false

主要关注配置文件中以下内容
```yaml
#集群名称，三台集群，要配置相同的集群名称！！！
cluster.name: my-application
#节点名称
node.name: node-1 #是不是有资格主节点
node.master: true
#是否存储数据
node.data: true
#最⼤集群节点数
node.max_local_storage_nodes: 3 #⽹关地址
network.host: 0.0.0.0
#端⼝
http.port: 9200
#内部节点之间沟通端⼝
transport.tcp.port: 9300
#es7.x 之后新增的配置，写⼊候选主节点的设备地址，在开启服务后可以被选为主节点
discovery.seed_hosts: ["localhost:9300","localhost:9400","localhost:9500"]
#es7.x 之后新增的配置，初始化⼀个新的集群时需要此配置来选举master
cluster.initial_master_nodes: ["node-1", "node-2","node-3"] #数据和存储路径
path.data: /Users/louis.chen/Documents/study/search/storage/a/data
path.logs: /Users/louis.chen/Documents/study/search/storage/a/logs
```


### 只有一个节点时，黄色的健康状态(yellow status)是正确的

[原文](https://discuss.elastic.co/t/cluster-yellow-reason-shards-started-kibana-0/93034)

How many nodes do you have? If you only have one node, yellow is expected. Yellow means that all the primaries are allocated, but one or more replicas are missing. Elasticsearch won't allocate replicas to the same node as the primary, so if you only have one node your cluster will be in yellow status.

It's not really a problem, just a notice to let you know.

### _analyze 接口
[原文](https://www.elastic.co/guide/en/elasticsearch/reference/7.12/indices-analyze.html)

Performs analysis on a text string and returns the resulting tokens.

```http
GET /_analyze
{
  "analyzer" : "standard",
  "text" : "Quick Brown Foxes!"
}
```

### mappings

```http
GET /publications/_mapping
{
  "properties": {
    "title":  { "type": "text"}
  }
}

结果
"test": {   // index 名字
    "mappings": {
        "properties": {
            "content": {    // 字段名
                "type": "text",
                "fields": {
                    "keyword": {
                        "type": "keyword",
                        "ignore_above": 256
                    }
                }
            },
```

