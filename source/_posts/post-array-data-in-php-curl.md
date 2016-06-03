---
title: PHP 发起 curl POST 请求时传递数组
date: 2016-06-03T10:42:03.000Z
tags:
  - curl
  - php
  - tips
categories:
  - PHP
---

使用 PHP 的 curl 可以发起 HTTP 外部请求，但是发起 POST 请求时，是无法直接传递数组的，从 curl 层面来说，也没有所谓的数组的概念，而更加通用也更合理的传递数据的格式其实是键值对（key value pair）。

那么，我们先得知道，为什么要传递数组。

HTTP 协议规定了 HTTP 请求的三个部分：状态行、请求头、消息主体。消息主体实际上是没有规定格式的。平常主要用到的几个请求头 Content-Type 为

```markdown
application/x-www-form-urlencoded
multipart/form-data
application/json
text/xml
```

所以问题的答案很明白了，传递什么样的数据类型得看需要发送什么样的请求。

一个典型的 curl POST 请求是下面这样：

```bash
 curl -X POST --data 'params[]=check1&params[]=check2' 'http://jayxhj.com/test/curl.php'
```

上面的请求将发送一个 Content-Type 为 application/x-www-form-urlencoded 的请求，请求的 body 为 `params[]=check1&params[]=check2` ，在服务端，只需使用 `$_POST` 即可获取。

那么回到 curl ，我们只需设置 option 为 **CURLOPT_POSTFIELDS** 的 VALUE 为 key/value pair ，即可将数组以字符串的形式传递至服务端，并直接由 `$_POST` 获取。

```php
<?php
$curl = curl_init();

curl_setopt($curl, CURLOPT_URL, 'http://jayxhj.com/test/curl.php');
curl_setopt($curl, CURLOPT_HEADER, 0);
curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);

$array = [
    'jayxhj',
    'pt'
];
$str   = http_build_query($array);

curl_setopt($curl, CURLOPT_POSTFIELDS, $str);
$data = curl_exec($curl);

curl_close($curl);

var_dump($data);
```
