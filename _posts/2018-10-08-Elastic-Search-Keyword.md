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

