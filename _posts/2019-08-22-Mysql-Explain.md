1.id介绍
这个id不是主键的意思，他是用来标识select查询的序列号，包含一组数字，表示查询中执行select子句或者操作表的顺序。

会出现以下情况：

id相同：按从上到下顺序执行

id不同：id值越大，优先级越高，越先被执行

id相同不同的同时存在：优先执行id值大的，如果id值相同，则按从上到下的顺序执行

id为null表示是用来合并结果集的，在sql使用union关键字合并结果集就会出现他。

2.select_type介绍
顾名思义，表示查询的类型

类型	说明
simple	简单子查询，不包含子查询和union
primary	包含union或者子查询，最外层的部分标记为primary
subquery	一般子查询中的子查询被标记为subquery，也就是位于select列表中的查询
derived	派生表——该临时表是从子查询派生出来的，位于form中的子查询
union	位于union中第二个及其以后的子查询被标记为union，第一个就被标记为primary如果是union位于from中则标记为derived
union result	用来从匿名临时表里检索结果的select被标记为union result
dependent union	顾名思义，首先需要满足UNION的条件，及UNION中第二个以及后面的SELECT语句，同时该语句依赖外部的查询
subquery	子查询中第一个SELECT语句
dependent subquery	和DEPENDENT UNION相对UNION一样
3.table介绍
对应行正在访问哪一个表，表名或者别名。也有可能是临时表等等，或者是union合并结果集。

表名是derivedN的形式，表示使用了id为N的查询产生的衍生表
当有union result的时候，表名是union n1,n2等的形式，n1,n2表示参与union的id
注意：MySQL对待这些表和普通表一样，但是这些“临时表”是没有任何索引的。


 ———————————————— 
版权声明：本文为CSDN博主「UFO___」的原创文章，遵循CC 4.0 by-sa版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/UFO___/article/details/80951869

### select_type 

```mysql

# 子查询性能不如关联查询

# 关联查询
explain select 
a.Name as Employee 
from Employee a join Employee b  on a.ManagerId = b.Id and a.Salary > b.Salary

1	SIMPLE	a	NULL	ALL	NULL	NULL	NULL	NULL	4	100.00	Using where
1	SIMPLE	b	NULL	eq_ref	PRIMARY	PRIMARY	4	graph.a.ManagerId	1	33.33	Using where

INSERT INTO `` (`id`, `select_type`, `table`, `partitions`, `type`, `possible_keys`, `key`, `key_len`, `ref`, `rows`, `filtered`, `Extra`)
VALUES
	(1, 'SIMPLE', 'a', NULL, 'ALL', NULL, NULL, NULL, NULL, 4, 100.00, 'Using where'),
	(1, 'SIMPLE', 'b', NULL, 'eq_ref', 'PRIMARY', 'PRIMARY', '4', 'graph.a.ManagerId', 1, 33.33, 'Using where');


# 子查询
explain select f.Name as Employee from Employee f where f.ManagerId is not null and f.Salary > (select s.Salary from Employee s where s.Id=f.ManagerId)

1	PRIMARY	f	NULL	ALL	NULL	NULL	NULL	NULL	4	75.00	Using where
2	DEPENDENT SUBQUERY	s	NULL	eq_ref	PRIMARY	PRIMARY	4	graph.f.ManagerId	1	100.00	NULL

INSERT INTO `` (`id`, `select_type`, `table`, `partitions`, `type`, `possible_keys`, `key`, `key_len`, `ref`, `rows`, `filtered`, `Extra`)
VALUES
	(1, 'PRIMARY', 'f', NULL, 'ALL', NULL, NULL, NULL, NULL, 4, 75.00, 'Using where'),
	(2, 'DEPENDENT SUBQUERY', 's', NULL, 'eq_ref', 'PRIMARY', 'PRIMARY', '4', 'graph.f.ManagerId', 1, 100.00, NULL);

# 另一个例子
explain select inn.e as Email from (select email as e, count(email) as c from user_obs u group by u.email) as inn where inn.c > 1

explain select email from user_obs group by email having count(email) > 1

```

用子查询的时候，select_type 会出现primary 和dependent subquery，这两个非常慢，而用关联查询(join)，则select_type 只有simple 类型的。
