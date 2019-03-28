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

