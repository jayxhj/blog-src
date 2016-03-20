---
title: 反引号( ` )  与 单引号( ' ) 以及 双引号( '' ) 的使用
date: 2016-03-20 18:32:36
tags:
  - MySQL
  - PHP
  - quote
  
---

反引号 `` ` `` (backtick) 与单引号 `'`(single quote) 以及双引号 `"` (double quote) 在 PHP 及 MySQL 中都有不同的含义，下面将分别说明并给出它们在 PHP 和 MySQL 结合使用时的一些注意事项。

## 在 PHP 中
`` ` `` 在 PHP 中是 [执行运算符](http://php.net/manual/zh/language.operators.execution.php)，效果与调用 [shell_exec()](http://php.net/manual/zh/function.shell-exec.php)  相同 。

`'` 在 PHP 中用来输出字面量值，被单引号括起来的内容会被原样输出。

`"` 在 PHP 中可以用来解析变量。

##  在 MySQL 中
`` ` `` 被用来在查询，告诉解析器反引号内的内容表示一个字面量，直接读取而不用做变量替换。

`"` 与 `'` 用来均被用来解析 MySQL 字符串及 [特殊字符](http://dev.mysql.com/doc/refman/5.7/en/string-literals.html#character-escape-sequences) ，不过这与 MySQL 的 SQL mode 设置有关。当 `ANSI_QUOTES ` 开启时，`"` 将被当作标识符。

### SQL mode 
MySQL 中开启 ANSI mode 时将会使用 `'` 来告诉解析器，`'` 内包裹的字符串字面量直接读取，无需解析。

```sql
mysql> SET SESSION sql_mode ='ANSI_QUOTES';
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT 'hello', '"hello"', '""hello""', 'hel''lo', '\'hello','\r';
+-------+---------+-----------+--------+--------+---+
| hello | "hello" | ""hello"" | hel'lo | 'hello |   |
+-------+---------+-----------+--------+--------+---+
 |hello | "hello" | ""hello"" | hel'lo | 'hello |
+-------+---------+-----------+--------+--------+---+
1 row in set (0.01 sec)

mysql> SELECT "hello", '"hello"', '""hello""', 'hel''lo', '\'hello','\r';
ERROR 1054 (42S22): Unknown column 'hello' in 'field list'

mysql> SELECT hello, '"hello"', '""hello""', 'hel''lo', '\'hello','\r';
ERROR 1054 (42S22): Unknown column 'hello' in 'field list'

mysql> SELECT `hello`, '"hello"', '""hello""', 'hel''lo', '\'hello','\r';
ERROR 1054 (42S22): Unknown column 'hello' in 'field list'
```

## PHP 执行 MySQL 查询
为了防止 SQL 注入，最好使用预处理语句及参数绑定来执行查询。建议使用 [PDO](http://php.net/manual/en/book.pdo.php) 或者 [MySQLi](http://php.net/manual/en/book.mysqli.php)

* PDO    

    ```php
    <?php    
    $dbh = new PDO('mysql:host=localhost;dbname=test', $user, $pass);
    $stmt = $dbh->prepare("INSERT INTO REGISTRY (name, value) VALUES (:name, :value)");
    $stmt->bindParam(':name', $name);
    $stmt->bindParam(':value', $value);
    ```
    
* MySQLi

    ```php
    <?php
    $mysqli = new mysqli("localhost", "user", "password", "database");
    $stmt = $mysqli->prepare("INSERT INTO `test`(`id`) VALUES (?)");
    $stmt->bind_param("i", $id)
    ```
    
## 实践
SQL 标准规定使用 `'` 来包裹字符串，但不同的数据库有不同的实现，最好的方式，当然是使用 PDO ，这样就不用对 sql 语句单独进行处理，而且在转换数据库时业务逻辑中的 sql 也无需修改和转换。


最好的实践其实应该在代码设计上规避错误：

1. 取名不与 [MySQL 保留字](http://dev.mysql.com/doc/refman/5.5/en/reserved-words.html) 冲突 
2. 清楚 MySQL 服务器 SQL mode **`SELECT @@sql_mode`**
3. 使用 `` ` `` 包裹表名及列名
4. 使用 `'` 包裹查询条件
    
    
    



