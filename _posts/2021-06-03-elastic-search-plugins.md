### 中文分词插件

[ik](https://github.com/medcl/elasticsearch-analysis-ik/)
[smartcn](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-smartcn.html)

### mapper-size

[mapper-size](https://www.elastic.co/guide/en/elasticsearch/plugins/current/mapper-size.html#mapper-size-install)

[使用](https://fossies.org/linux/elasticsearch/docs/plugins/mapper.asciidoc)

The mapper-size plugin provides the _size metadata field which, when enabled, indexes the size in bytes of the original _source field.

Using the _size field
In order to enable the _size field, set the mapping as follows:

PUT my_index
{
  "mappings": {
    "_doc": {
      "_size": {
        "enabled": true
      }
    }
  }
}
The value of the _size field is accessible in queries, aggregations, scripts, and when sorting:

# Example documents
PUT my_index/_doc/1
{
  "text": "This is a document"
}

PUT my_index/_doc/2
{
  "text": "This is another document"
}

GET my_index/_search
{
  "query": {
    "range": {
      "_size": { (1)
        "gt": 10
      }
    }
  },
  "aggs": {
    "sizes": {
      "terms": {
        "field": "_size", (2)
        "size": 10
      }
    }
  },
  "sort": [
    {
      "_size": { (3)
        "order": "desc"
      }
    }
  ],
  "script_fields": {
    "size": {
      "script": "doc['_size']"  (4)
    }
  }
}
