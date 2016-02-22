---
title: 分组求和，将一列的数据求和
id: 296
categories:
  - MySQL
date: 2013-11-05 10:04:02
tags:
---

在数据库操作中常常会将某一列的具有相似性质的数据进行求和，昨天在做的项目中遇到了。
后来请教了一下自己又摸索了一下，得出了解决方案，那就是**分组求和**。
需求：将某列数据求和
例子：数据库内容为：
![数据库内容](http://images.cnitblog.com/blog/502093/201311/05100823-8881d57158a1458aa88c20fbe0a02682.png)

根据record_type进行分组再求和，也就是将1、2、3三种类型进行分组，所以结果预期为三行
![根据record_type分组](http://images.cnitblog.com/blog/502093/201311/05101102-0e51609e1b494e0b91d0480c9b61bcc4.png)

再一种是根据record_date来分组，由于日期有两组，故预期分组也为两组，结果为同一日期的c、d列数据相加
![根据record_date进行分组](http://images.cnitblog.com/blog/502093/201311/05101232-3342a407272040cbbd228885e608c4b4.png)

总结：当需要进行对某一列的具有相似条件（对应于where中的条件）的数据进行合并时，使用group by将记录分组，再使用聚合函数对数据进行需要的操作。这样就可以使用一条语句将所有的数据进行分组求和。

创建例子中的数据的SQL语句为
```sql
 CREATE DATABASE /*!32312 IF NOT EXISTS*/`group` /*!40100 DEFAULT CHARACTER SET utf8 */;
 USE `group`;
 /*Table structure for table `test` */ 
DROP TABLE IF EXISTS `test`;
CREATE TABLE `test` (
`id` int(10) NOT NULL AUTO_INCREMENT,
`record_date` date DEFAULT NULL, `record_type` int(10) DEFAULT NULL,
`c` int(10) DEFAULT NULL,
`d` int(10) DEFAULT NULL,
PRIMARY KEY (`id`)
 ENGINE=InnoDB AUTO_INCREMENT=13 DEFAULT CHARSET=utf8;

/*Data for the table `test` */
insert  into `test`(`id`,`record_date`,`record_type`,`c`,`d`) values (1,'2013-10-14',1,1000,10000),(2,'2013-10-15',1,2000,20000),(3,'2013-10-14',2,3000,30000),(4,'2013-10-15',2,4000,40000),(5,'2013-10-14',3,5000,50000);
```
查询语句为
```sql
SELECT SUM(c) 'c列数据的和',SUM(d)'d列数据的和'
FROM test
WHERE record_date BETWEEN '2013-10-13' AND '2013-10-16'    
GROUP BY record_type
ORDER BY record_type ASC
```
 