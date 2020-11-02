#### 1．索引作用

   在索引列上，除了上面提到的有序查找之外，数据库利用各种各样的快速定位技术，能够大大提高查询效率。特别是当数据量非常大，查询涉及多个表时，使用索引往往能使查询速度加快成千上万倍。

   例如，有3个未索引的表t1、t2、t3，分别只包含列c1、c2、c3，每个表分别含有1000行数据组成，指为1～1000的数值，查找对应值相等行的查询如下所示。

SELECT c1,c2,c3 FROM t1,t2,t3 WHERE c1=c2 AND c1=c3

   此查询结果应该为1000行，每行包含3个相等的值。在无索引的情况下处理此查询，必须寻找3个表所有的组合，以便得出与WHERE子句相配的那些行。而可能的组合数目为1000×1000×1000（十亿），显然查询将会非常慢。

   如果对每个表进行索引，就能极大地加速查询进程。利用索引的查询处理如下。

（1）从表t1中选择第一行，查看此行所包含的数据。

（2）使用表t2上的索引，直接定位t2中与t1的值匹配的行。类似，利用表t3上的索引，直接定位t3中与来自t1的值匹配的行。

（3）扫描表t1的下一行并重复前面的过程，直到遍历t1中所有的行。

   在此情形下，仍然对表t1执行了一个完全扫描，但能够在表t2和t3上进行索引查找直接取出这些表中的行，比未用索引时要快一百万倍。

   利用索引，MySQL加速了WHERE子句满足条件行的搜索，而在多表连接查询时，在执行连接时加快了与其他表中的行匹配的速度。

### 2.  创建索引

在执行CREATE TABLE语句时可以创建索引，也可以单独用CREATE INDEX或ALTER TABLE来为表增加索引。

#### 1．ALTER TABLE

ALTER TABLE用来创建普通索引、UNIQUE索引或PRIMARY KEY索引。

 

ALTER TABLE table_name ADD INDEX index_name (column_list)

ALTER TABLE table_name ADD UNIQUE (column_list)

ALTER TABLE table_name ADD PRIMARY KEY (column_list)

 

其中table_name是要增加索引的表名，column_list指出对哪些列进行索引，多列时各列之间用逗号分隔。索引名index_name可选，缺省时，MySQL将根据第一个索引列赋一个名称。另外，ALTER TABLE允许在单个语句中更改多个表，因此可以在同时创建多个索引。

#### 2．CREATE INDEX

CREATE INDEX可对表增加普通索引或UNIQUE索引。

 

CREATE INDEX index_name ON table_name (column_list)

CREATE UNIQUE INDEX index_name ON table_name (column_list)

 

table_name、index_name和column_list具有与ALTER TABLE语句中相同的含义，索引名不可选。另外，不能用CREATE INDEX语句创建PRIMARY KEY索引。

```
DROP INDEX cv_scene_favorites_user_id_scene_id ON cv_scene_favorites_v2

CREATE UNIQUE INDEX cv_scene_favorites_user_id_scene_id ON cv_scene_favorites_v2 (`user_id`,`scene_id`)
```

#### 3．索引类型

在创建索引时，可以规定索引能否包含重复值。如果不包含，则索引应该创建为PRIMARY KEY或UNIQUE索引。对于单列惟一性索引，这保证单列不包含重复的值。对于多列惟一性索引，保证多个值的组合不重复。

PRIMARY KEY索引和UNIQUE索引非常类似。事实上，PRIMARY KEY索引仅是一个具有名称PRIMARY的UNIQUE索引。这表示一个表只能包含一个PRIMARY KEY，因为一个表中不可能具有两个同名的索引。

下面的SQL语句对students表在sid上添加PRIMARY KEY索引。

 

ALTER TABLE students ADD PRIMARY KEY (sid)

### 4.  删除索引

可利用ALTER TABLE或DROP INDEX语句来删除索引。类似于CREATE INDEX语句，DROP INDEX可以在ALTER TABLE内部作为一条语句处理，语法如下。

 

DROP INDEX index_name ON talbe_name

ALTER TABLE table_name DROP INDEX index_name

ALTER TABLE table_name DROP PRIMARY KEY

 

其中，前两条语句是等价的，删除掉table_name中的索引index_name。

第3条语句只在删除PRIMARY KEY索引时使用，因为一个表只可能有一个PRIMARY KEY索引，因此不需要指定索引名。如果没有创建PRIMARY KEY索引，但表具有一个或多个UNIQUE索引，则MySQL将删除第一个UNIQUE索引。

如果从表中删除了某列，则索引会受到影响。对于多列组合的索引，如果删除其中的某列，则该列也会从索引中删除。如果删除组成索引的所有列，则整个索引将被删除。

**5．查看索引**

mysql> show index from tblname;

mysql> show keys from tblname;

　　· Table
　　表的名称。
　　· Non_unique
　　如果索引不能包括重复词，则为0。如果可以，则为1。
　　· Key_name
　　索引的名称。
　　· Seq_in_index
　　索引中的列序列号，从1开始。
　　· Column_name
　　列名称。
　　· Collation
　　列以什么方式存储在索引中。在MySQL中，有值‘A’（升序）或NULL（无分类）。
　　· Cardinality
　　索引中唯一值的数目的估计值。通过运行ANALYZE TABLE或myisamchk -a可以更新。基数根据被存储为整数的统计数据来计数，所以即使对于小型表，该值也没有必要是精确的。基数越大，当进行联合时，MySQL使用该索引的机会就越大。
　　· Sub_part
　　如果列只是被部分地编入索引，则为被编入索引的字符的数目。如果整列被编入索引，则为NULL。
　　· Packed
　　指示关键字如何被压缩。如果没有被压缩，则为NULL。
　　· Null
　　如果列含有NULL，则含有YES。如果没有，则该列含有NO。
　　· Index_type
　　用过的索引方法（BTREE, FULLTEXT, HASH, RTREE）。
　　· Comment



### MySql在建立索引优化时需要注意的问题

设计好MySql的索引可以让你的数据库飞起来，大大的提高数据库效率。设计MySql索引的时候有一下几点注意：

1，创建索引

对于查询占主要的应用来说，索引显得尤为重要。很多时候性能问题很简单的就是因为我们忘了添加索引而造成的，或者说没有添加更为有效的索引导致。如果不加

索引的话，那么查找任何哪怕只是一条特定的数据都会进行一次全表扫描，如果一张表的数据量很大而符合条件的结果又很少，那么不加索引会引起致命的性能下

降。但是也不是什么情况都非得建索引不可，比如性别可能就只有两个值，建索引不仅没什么优势，还会影响到更新速度，这被称为过度索引。

2，复合索引

比如有一条语句是这样的：select * from users where area=’beijing’ and age=22;

如果我们是在area和age上分别创建单个索引的话，由于mysql查询每次只能使用一个索引，所以虽然这样已经相对不做索引时全表扫描提高了很多效

率，但是如果在area、age两列上创建复合索引的话将带来更高的效率。如果我们创建了(area, age,

salary)的复合索引，那么其实相当于创建了(area,age,salary)、(area,age)、(area)三个索引，这被称为最佳左前缀

特性。因此我们在创建复合索引时应该将最常用作限制条件的列放在最左边，依次递减。

3，索引不会包含有NULL值的列

只要列中包含有NULL值都将不会被包含在索引中，复合索引中只要有一列含有NULL值，那么这一列对于此复合索引就是无效的。所以我们在数据库设计时不要让字段的默认值为NULL。

4，使用短索引

对串列进行索引，如果可能应该指定一个前缀长度。例如，如果有一个CHAR(255)的 列，如果在前10 个或20 个字符内，多数值是惟一的，那么就不要对整个列进行索引。短索引不仅可以提高查询速度而且可以节省磁盘空间和I/O操作。

5，排序的索引问题

mysql查询只使用一个索引，因此如果where子句中已经使用了索引的话，那么order by中的列是不会使用索引的。因此数据库默认排序可以符合要求的情况下不要使用排序操作；尽量不要包含多个列的排序，如果需要最好给这些列创建复合索引。

6，like语句操作

一般情况下不鼓励使用like操作，如果非使用不可，如何使用也是一个问题。like “%aaa%” 不会使用索引而like “aaa%”可以使用索引。

7，不要在列上进行运算

select * from users where

YEAR(adddate)

8，不使用NOT IN和操作

NOT IN和操作都不会使用索引将进行全表扫描。NOT IN可以NOT EXISTS代替，id3则可使用id>3 or id


### 聚簇索引与非聚簇索引

[原文](https://www.jianshu.com/p/fa8192853184)

聚簇索引：将数据存储与索引放到了一块，找到索引也就找到了数据
非聚簇索引：将数据存储于索引分开结构，索引结构的叶子节点指向了数据的对应行，myisam通过key_buffer把索引先缓存到内存中，当需要访问数据时（通过索引访问数据），在内存中直接搜索索引，然后通过索引找到磁盘相应数据，这也就是为什么索引不在key buffer命中时，速度慢的原因


聚簇索引默认是主键，如果表中没有定义主键，InnoDB 会选择一个唯一的非空索引代替。如果没有这样的索引，InnoDB 会隐式定义一个主键来作为聚簇索引。InnoDB 只聚集在同一个页面中的记录。包含相邻健值的页面可能相距甚远。如果你已经设置了主键为聚簇索引，必须先删除主键，然后添加我们想要的聚簇索引，最后恢复设置主键即可。

刚才说到了，聚簇索引性能最好而且具有唯一性，所以非常珍贵，必须慎重设置。一般要根据这个表最常用的SQL查询方式来进行选择，某个字段作为聚簇索引，或组合聚簇索引，这个要看实际情况。

1. InnoDB使用的是聚簇索引，将主键组织到一棵B+树中，而行数据就储存在叶子节点上，若使用"where id = 14"这样的条件查找主键，则按照B+树的检索算法即可查找到对应的叶节点，之后获得行数据。
2. 若对Name列进行条件搜索，则需要两个步骤：第一步在辅助索引B+树中检索Name，到达其叶子节点获取对应的主键。第二步使用主键在主索引B+树种再执行一次B+树检索操作，最终到达叶子节点即可获取整行数据。（重点在于通过其他键需要建立辅助索引）

MyISM使用的是非聚簇索引，非聚簇索引的两棵B+树看上去没什么不同，节点的结构完全一致只是存储的内容不同而已，主键索引B+树的节点存储了主键，辅助键索引B+树存储了辅助键。表数据存储在独立的地方，这两颗B+树的叶子节点都使用一个地址指向真正的表数据，对于表数据来说，这两个键没有任何差别。由于索引树是独立的，通过辅助键检索无需访问主键的索引树。

#### 劣势

MyISM使用的是非聚簇索引，非聚簇索引的两棵B+树看上去没什么不同，节点的结构完全一致只是存储的内容不同而已，主键索引B+树的节点存储了主键，辅助键索引B+树存储了辅助键。表数据存储在独立的地方，这两颗B+树的叶子节点都使用一个地址指向真正的表数据，对于表数据来说，这两个键没有任何差别。由于索引树是独立的，通过辅助键检索无需访问主键的索引树。

为什么主键通常建议使用自增id
聚簇索引的数据的物理存放顺序与索引顺序是一致的，即：只要索引是相邻的，那么对应的数据一定也是相邻地存放在磁盘上的。如果主键不是自增id，那么可以想 象，它会干些什么，不断地调整数据的物理地址、分页，当然也有其他一些措施来减少这些操作，但却无法彻底避免。但，如果是自增的，那就简单了，它只需要一 页一页地写，索引结构相对紧凑，磁盘碎片少，效率也高。

因为MyISAM的主索引并非聚簇索引，那么他的数据的物理地址必然是凌乱的，拿到这些物理地址，按照合适的算法进行I/O读取，于是开始不停的寻道不停的旋转。聚簇索引则只需一次I/O。（强烈的对比）

不过，如果涉及到大数据量的排序、全表扫描、count之类的操作的话，还是MyISAM占优势些，因为索引所占空间小，这些操作是需要在内存中完成的。

mysql中聚簇索引的设定
聚簇索引默认是主键，如果表中没有定义主键，InnoDB 会选择一个唯一的非空索引代替。如果没有这样的索引，InnoDB 会隐式定义一个主键来作为聚簇索引。InnoDB 只聚集在同一个页面中的记录。包含相邻健值的页面可能相距甚远。

### 联合索引

```
explain SELECT * FROM table_item WHERE user_id = 2 ORDER BY item_id LIMIT 0, 5
```

如果user_id 和item_id 分别都有单独的索引，那么，这块还是比较慢，会`Using where; Using filesort`
所以最好建立一个(user_id, item_id) 的联合索引

1. 一条 SQL 语句只能使用 1 个索引 (5.0-)，MySQL 根据表的状态，选择一个它认为最好的索引用于优化查询
2. 联合索引，只能按从左到右的顺序依次使用

MySQL 选择了 user_id，那么 item_id 的索引没有起到任何用处
修改表结构，删除 user_id 和 item_id 的 INDEX 索引，建立一个名为 user_item 的联合 UNIQUE 索引，顺序是先 user_id 后 item_id，再 EXPLAIN，这回只有 Using where 了。

首先，只有在order by 数据列的时候才会出现using filesort，而且如果你不对进行order by的这一列设置索引的话，无论列值是否有相同的都会出现using filesort。因此，只要用到order by 的这一列都应该为其建立一个索引。
```
SELECT * FROM DB.TB WHERE ID=2000010000 AND FID IN (9,8,3,13,38,40) ORDER BY INVERSE_DATE LIMIT 0, 5
```
里面建立的索引为一个三列的多列索引：IDX（ID,FID ,INVERSE_DATE） 。INVERSE_DATE这个是时间的反向索引。
对于这个sql我当时最开始认为应该是个优化好的状态，应该没有什么纰漏了，结果一explain才发现竟然出现了：Using where; Using filesort。
汗。。。为什么呢，后来经过分析才得知，原来在多列索引在建立的时候是以B-树结构建立的，因此建立索引的时候是先建立ID的按顺序排的索引，在相同ID的情况下建立FID按 顺序排的索引，最后在FID 相同的情况下建立按INVERSE_DATE顺序排的索引，如果列数更多以此类推。有了这个理论依据我们可以看出在这个sql使用这个IDX索引的时候只是用在了order by之前，order by INVERSE_DATE 实际上是using filesort出来的。。汗死了。。因此如果我们要在优化一下这个sql就应该为它建立另一个索引IDX（ID,INVERSE_DATE），这样就消除了using filesort速度也会快很多。。问题终于解决了。。- -！


1）explain select id from course where category_id>1 order by category_id;

根据最左前缀原则，order by后面的的category_id会用到组合索引

（2）explain select id from course where category_id>1 order by category_id,buy_times;

根据最左前缀原则，order by后面的的category_id buy_times会用到组合索引，因为索引就是这两个字段

（3）explain select id from course where category_id>1 order by buy_times;

根据最左前缀原则，order by后面的字段是缺少了最左边的category_id，所以会产生 using filesort

（4）explain select id from course where category_id>1 order by buy_times,category_id;

order by后面的字段顺序不符合组合索引中的顺序，所以order by后面的不会走索引，即会产生using filesort

（5）explain select id from course order by category_id;

根据最左前缀原则，order by后面存在索引中的最左列，所以会用到索引

（6）explain select id from course order by buy_times;

根据最左前缀原则，order by后面的字段 没有索引中的最左列的字段，所以不会走索引，会产生using filesort

（7）explain select id from course where buy_times > 1 order by buy_times;

根据最左前缀原则，order by后面的字段 没有索引中的最左列的字段，所以不会走索引，会产生using fillesort

（8）explain select id from course where buy_times > 1 order by category_id;

根据最左前缀原则，order by后面的字段存在于索引中最左列，所以会走索引

（9）explain select id from course order by buy_times desc,category_id asc;

根据最最左前缀原则，order by后面的字段顺序和索引中的不符合，则会产生using filesort

（10）explain select id from course order by category_id desc,buy_times asc;

这一条虽然order by后面的字段和索引中字段顺序相同，但是一个是降序，一个是升序，所以也会产生using filesort，同时升序和同时降序就不会产生using filesort了

总结：终上所述，（3）（4）（6）（7）（9）（10）都会产生using filesort.


### COLLATE=utf8mb4_0900_ai_ci

所谓utf8_unicode_ci，其实是用来排序的规则。对于mysql中那些字符类型的列，如VARCHAR，CHAR，TEXT类型的列，都需要有一个COLLATE类型来告知mysql如何对该列进行排序和比较。简而言之，COLLATE会影响到ORDER BY语句的顺序，会影响到WHERE条件中大于小于号筛选出来的结果，会影响**DISTINCT**、**GROUP BY**、**HAVING**语句的查询结果。另外，mysql建索引的时候，如果索引列是字符类型，也会影响索引创建，只不过这种影响我们感知不到。总之，凡是涉及到字符类型比较或排序的地方，都会和COLLATE有关。

mysql中有utf8和utf8mb4两种编码，在mysql中请大家忘记**utf8**，永远使用**utf8mb4**。这是mysql的一个遗留问题，mysql中的utf8最多只能支持3bytes长度的字符编码，对于一些需要占据4bytes的文字，mysql的utf8就不支持了，要使用utf8mb4才行。

很多COLLATE都带有_ci字样，这是Case Insensitive的缩写，即大小写无关，也就是说"A"和"a"在排序和比较的时候是一视同仁的。selection * from table1 where field1="a"同样可以把field1为"A"的值选出来。与此同时，对于那些_cs后缀的COLLATE，则是Case Sensitive，即大小写敏感的。

在mysql中使用show collation指令可以查看到mysql所支持的所有COLLATE。以utf8mb4为例，该编码所支持的所有COLLATE如下图所示。

从mysql 8.0开始，mysql默认的CHARSET已经不再是Latin1了，改为了utf8mb4，并且默认的COLLATE也改为了utf8mb4_0900_ai_ci。utf8mb4_0900_ai_ci大体上就是unicode的进一步细分，0900指代unicode比较算法的编号（ Unicode Collation Algorithm version），ai表示accent insensitive（发音无关），例如e, è, é, ê 和 ë是一视同仁的。

设置COLLATE可以在示例级别、库级别、表级别、列级别、以及SQL指定。实例级别的COLLATE设置就是mysql配置文件或启动指令中的collation_connection系统变量。

库级别设置COLLATE的语句如下：

```mysql
CREATE DATABASE <db_name> DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

如果库级别没有设置CHARSET和COLLATE，则库级别默认的CHARSET和COLLATE使用实例级别的设置。在mysql8.0以下版本中，你如果什么都不修改，默认的CHARSET是Latin1，默认的COLLATE是latin1_swedish_ci。从mysql8.0开始，默认的CHARSET已经改为了utf8mb4，默认的COLLATE改为了utf8mb4_0900_ai_ci。

作者：老男孩_Misaya
链接：https://www.jianshu.com/p/f8707b8461d3
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
