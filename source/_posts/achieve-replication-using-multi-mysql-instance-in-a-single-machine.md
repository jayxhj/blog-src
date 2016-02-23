---
title: MySQL 单机多实例主从
tags:
 - Master
 - Slave
id: 401
categories:
 - MySQL
date: 2015-09-22 22:37:32
---

## environment

  [root@iZ25qtxg0q6Z ~]# uname -a
  Linux iZ25qtxg0q6Z 2.6.32-431.23.3.el6.x86_64 #1 SMP Thu Jul 31 17:20:51 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
  [root@iZ25qtxg0q6Z ~]# cat /etc/redhat-release
  CentOS release 6.7 (Final)
  [root@iZ25qtxg0q6Z ~]# mysql --version
  mysql Ver 14.14 Distrib 5.1.73, for redhat-linux-gnu (x86_64) using readline 5.1
  

## set-up replication steps
1. enable binary logging of master
2. stop writing `FLUSH TABLES WITH READ LOCK;`
3. obtain binary log file name and position for slave
4. transfer data from master to slave (mysqldump 、 mysqlimport)
5. on the master `UNLOCK TABLES;`
6. Setting the Replication Slave Configuration
```sql
CHANGE MASTER TO
MASTER_HOST='master_host_name',
MASTER_USER='replication_user_name',
MASTER_PASSWORD='replication_password',
MASTER_LOG_FILE='recorded_log_file_name',
MASTER_LOG_POS=recorded_log_position;
```
7. check master and slave status `SHOW VARIABLES LIKE 'server_id'`
8. in slave `START SLAVE`

## 安装过程中的命令及问题解决方法

### mysql_secure_installation

mysql_secure_installation 作用：

* set root password,
* disallowing root login remotely,
* removing anonymous user accounts after first installation
* removing test database which can be accessed by any users

但是 mysql_secure_installation 无法指定命令参数（[5.7](https://dev.mysql.com/doc/refman/5.1/en/mysql-secure-installation.html) 可以指定参数以后可以直接指定，之前的版本无法指定），会使用默认的配置，导致新安装的实例安全配置无法更新
配置位置 `/var/lib/mysql/mysql2.sock`

有两种方法可以解决执行 mysql_secure_installation 时应用到指定实例：
  `[root@iZ25qtxg0q6Z ~]# which mysql_secure_installation
  /usr/bin/mysql_secure_installation`

1. 在配置文件中制定，修改为对应的实例配置
2. 使用[软链](http://dba-valley.blogspot.jp/2013/03/mysql-secure-installation-for-non.html)

### 启动 mysql instanse

`mysqld_safe --defaults-file=/etc/my2.cnf`

### 主从数据保持一致（导出导入）

1. 导出数据 `mysqldump --all-databases --master-data --events &gt; dbdump.db -p`
2. 导入至指定实例 `mysqlimport -uroot -P 3380 -h 127.0.0.1 dbdump.db -p`

### stop mysql instance by specifing sockct

`mysqladmin -S /var/lib/mysql/mysql2.sock shutdown -p`

## userful links

1. [mysql 安全设置](http://stackoverflow.com/questions/15439307/mysql-secure-installation-cant-connect-to-local-mysql-server-through-socket)
2. [使用软链](http://dba-valley.blogspot.jp/2013/03/mysql-secure-installation-for-non.html)
3. [kill process](http://www.cyberciti.biz/faq/kill-process-in-linux-or-terminate-a-process-in-unix-or-linux-systems/)
4. [config slave error handlering](http://www.xaprb.com/blog/2007/08/01/why-mysql-server-not-configured-as-slave/)
5. [restore mysqldump file](http://stackoverflow.com/questions/105776/how-do-i-restore-a-mysql-dump-file)
6. [reset mysql root password](http://www.ghostchina.com/how-to-reset-mysqls-root-password/)
7. [create-multiple-mysql-instance](http://sharadchhetri.com/2013/12/02/create-multiple-mysql-instance-centos-6-4-red-hat-6-4/)
8. [multiple-mysql-servers](https://dev.mysql.com/doc/refman/5.1/en/multiple-unix-servers.html)
9. [Setting Up Replication with New Master and Slaves](https://dev.mysql.com/doc/refman/5.1/en/replication-howto-newservers.html)