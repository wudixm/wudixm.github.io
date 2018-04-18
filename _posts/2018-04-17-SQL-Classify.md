分类
id: 主键数字自增
name: 字符串
parent_id: 数字，父分类的 id
level: 数字分类级别，1级，2级 分类 （看看有没有用）
parent_path: 父分类的路径，从根节点 到父节点的 id 用 _ 拼接 （只有 2 级貌似用不上这个字段，再多级的话筛选多层级可能有用）

分类群组关系：
id: 数字 主键自增无意义
category_id: 数字 分类id
categroy_path: 字符串 分类从根节点到当前分类节点的 id 用 _ 拼接
group_id：数字 群组 id

查找分类：前缀匹配，categroy_path like 'id1_id2_%'