```
Database changed
mysql> insert ignore into cv_scene_favorites_v2 (`user_id`,`scene_id`,`created_at`)  select user_id, scene_id, created_at from cv_scene_holds ;
Query OK, 69900758 rows affected, 47526 warnings (6 hours 5 min 29.21 sec)
Records: 69948284  Duplicates: 47526  Warnings: 47526

mysql>

```

### update

```
update A, B  set A.mc = B.mc where A.bmbh = B.bmbh and A.xmbh = B.xmbh;
```

或

```
update A INNER JOIN B ON A.bmbh = B.bmbh AND A.xmbh = B.xmbh SET A.mc = B.mc;
```
### mysql 如何用一条SQL将一张表里的数据插入到另一张表

#### 1. 表结构完全一样

```
 insert into 表1
  select * from 表2
```

####  2. 表结构不一样（这种情况下得指定列名）

```
  insert into 表1 (列名1,列名2,列名3)
  select  列1,列2,列3 from 表2
```

#### 3、只从另外一个表取部分值

```
insert into 表1 (列名1,列名2,列名3) values(列1,列2,(select 列3 from 表2));
```