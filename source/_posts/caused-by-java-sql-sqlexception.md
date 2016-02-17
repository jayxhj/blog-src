---
title: 'Caused by: java.sql.SQLException: 无法转换为内部表示解决方法'
tags:
  - Hibernate
  - SQLException
  - 无法转化为内部格式
id: 200
categories:
  - Oracle
date: 2013-05-29 20:05:47
---

Hibernate 应用有三种开发方式：

1. 自底向上从数据库到持久化类；
2. 自顶向下从持久化类到数据库表；
3. 从中间出发向上和向下同时发展。                                                  
如果从下往上开发，使用 Database Explorer 反向生成 PO，其中的配置文件并不那么智能，比如如下规则

NUMBER(1)～NUMBER(18) 都可以使用 Long 来映射 
一般 NUMBER(1)～NUMBER(9) 使用 Integer 映射 
NUMBER(10)～NUMBER(18) 使用 Long 来映射 
大于 NUMBER(18) 的只能用 BigInteger（出自 http://bbs.csdn.net/topics/310225381）

这些规则实际上是不会反映到配置文件中去的 
所以会导致数据库里的字段类型与 Java里映射该字段属性的类型不能对应转换，出现 java.sql.SQLException

一般这种情况下你得检查你的配置文件

* java类型与数据库类型是否匹配？（参看 [oracle数据类型和对应的java类型](http://blog.csdn.net/perny/article/details/7971003)）

* 其他POJO是否与当前所查找的属性有关联，有那么在配置文件中会有一对多或多对一的关系，修改为对应的java类型即可

* 在POJO中属性的数据类型为Long，但是这个属性的getxxx()方法返回的类型却为long，而非Long，程序不会报错，但运行时会出错。（如果对数据库进行操作时，没有涉及到的属性，即使配置文件或者POJO数据类型出错也不会有问题，但一旦涉及，若类型不匹配，则会出现错误）

建议反向工程生成PO时检查数据类型，设置对应格式。
