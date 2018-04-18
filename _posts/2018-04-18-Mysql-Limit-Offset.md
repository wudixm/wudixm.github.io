MySQL里对LIMIT OFFSET的处理方式是，取出OFFSET+LIMIT的所有数据，然后去掉OFFSET，返回底部的LIMIT。
所以，在OFFSET数值较大时，MySQL的查询性能会非常低。

```
SELECT * FROM table_name ORDER BY id ASC LIMIT 10 OFFSET 10
```

MySQL会取出20条数据，只返回后10条。

```
SELECT * FROM table_name WHERE id > 10 ORDER BY id ASC LIMIT 10
```

利用id索引，MySQL只取出10个结果，返回。这种做法却是要求连续翻页，不能跳页，受约束。