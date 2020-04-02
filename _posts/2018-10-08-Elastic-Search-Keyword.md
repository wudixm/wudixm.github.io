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
