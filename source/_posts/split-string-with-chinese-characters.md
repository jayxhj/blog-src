---
title: substr()与mb_substr()的区别
tags:
  - mb_strcut()
  - mb_substr()
  - PHP
id: 304
categories:
  - PHP
date: 2013-11-23 15:36:23
---

这两个函数都是获取子字符串，而 mb_substr() 一般在字符串中包含中文的情况下使用。其中有个很重要的区别是 mb_substr() 按字来切分字符串，不管中英文。
例子如下
```php
<?php
$operation="中文test英文abc";
var_dump(strlen($operation));
$price = mb_substr ( $operation, 0, strlen ( $operation ), 'utf-8' );
var_dump($price);
$price = mb_substr ( $operation, 0, strlen ( $operation )-8, 'utf-8' );
var_dump($price);
?>
```

//输出为
```php
int 19
string ‘中文test英文abc’ (length=19)
string ‘中文test英文abc’ (length=19)
```
大家有没有注意到，第二个函数我使用的是的 $length 参数长度为字符串长减 8 啊，为何结果却一样呢？

这就是我今天要说的区别：mb_substr()将字符按字符数读取，故读取字符串”中文test英文abc”的长度为11，而实际上中文字符串占三个字节，所以输出长度为19。

如果需要按字节来截取包含中文的字符串可以使用 mb_strcut() 函数。另外需要注意的是mb开头的函数都必须启用 PHP 的 php_mbstring.dll 扩展才可使用。