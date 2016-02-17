---
title: MySQL索引类型
tags:
  - index
  - MySQL
id: 388
categories:
  - MySQL
date: 2014-08-08 16:07:59
---

---
有必要对数据库的索引类型及其实现做个了解及总结。

###Question:
1.  书中所说索引分为单列索引，主键索引以及联合索引；
2.  在数据库的GUI软件中却可以对一个表设置三种类型的索引：分别为normal、unique、full text；
3.  在MySQL manual中创建index的语法为
```
CREATE [UNIQUE|FULLTEXT|SPATIAL] INDEX index_name
[USING index_type]    
ON tbl_name (index_col_name,...)    
index_col_name:
col_name [(length)] [ASC | DESC]
```

###Answer:
疑惑解决:第一种分法是按照列的数量进行分类，第二、三种是按照索引类型进行分类

- normal index为普通索引
- primary index为主键索引，索引列的值必须唯一且不为空
- unique index为唯一索引，索引列必须唯一但可以为空
- fulltext index为全文索引，只能对CHAR,VARCHAR和TEXT列编制索引，并且只能在MyISAM表中编制
- spatial index为空间索引，只能对空间列编制索引，并且只能在MyISAM表中编制

###续
有时间再对其具体实现及背后使用的算法做了解及总结。
可能的关键字如下：
1. HASH
2. B-TREE
3. B+TREE