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