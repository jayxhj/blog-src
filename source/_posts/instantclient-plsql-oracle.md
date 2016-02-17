---
title: 使用instantclient和PL/SQL连接oracle远程数据库
tags:
  - instantclient
  - Oracle
id: 195
categories:
  - Oracle
date: 2013-05-13 19:22:50
---

**<a>PLSQL连接Oracle</a>**

Windows装Oracle 11g ，PLSQL Developer使用出现以下问题：

1、Database下拉框为空：

2、强制输入用户名、密码及Database，登录弹出：

错误内容引用

Initialzation error

Could not initialize

&quot;....&quot;

Make sure you have the 32 bits Oracle Client installed.

OracleHomeKey:

OracleHomeDir:...

Found:oci.dll

Using:

...

Loadlibrary(...)

returned 0

**解决办法**：

1、 **下载32位Oracle客户端**

下载链接：<u>http://www.oracle.com/technetwork/cn/topics/winsoft-095945-zhs.html</u>

下载<span>Instant Client 程序包就可以了。</span>

下载需要登录，得先在Oracle注册账号才能下载！

2、 **解压软件**

将下载到的instant client解压，比如解压到到D:\instantclient_11_2

3、 **设置PLSQL Developer**

在工具-首选项，连接，OCI库输入

D:\instantclient_11_2\oci.dll

4、 **建立连接信息**

tnsnames.ora，并保存到安装目录：D:\instantclient_11_2

tnsnames.ora内容如下
```
SERVICE_NAME=
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = host_ip)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = SERVICE_NAME)
    )
  )
```

需要修改的内容：SERVICE_NAME为服务名，host_ip为远程服务器的ip，若为本地服务器在本地则为127.0.0.1或者localhost；

5、 **添加环境变量**

添加环境变量系统变量中添加2个：第一个是指向TNS文件所在目录的，这个目录是你安装的Oracle的TNS文件所在目录。TNS文件就是保存了连接信息的文件。

**TNS_ADMIN** 值： D:\instantclient_11_2\

第二个是指定数据库使用的编码。如果不设置成以下值，那么连接上数据库后，你看到的所有中文的内容将会是乱码，都是一堆问号。

**NLS_LANG** 值：SIMPLIFIED CHINESE_CHINA.ZHS16GBK

这样就可以远程连接Oracle服务器了，也可以解决使用PL/SQL远程登陆时下拉列表里没有数据库的问题。