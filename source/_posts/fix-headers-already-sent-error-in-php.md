---
title: 解决 PHP "headers already sent" 错误
date: 2016-03-09 13:32:16
tags:
 - PHP
 - header()
 - HTTP
categories:
 - 翻译文章
 
---

这篇文章是 stackoverflow 上的一个问题的回答，答案集中了其它答案的想法，并做了归纳，所以翻译过来，以后遇到 "headers already sent" 的错误就可以直接一一排查，或者干脆在代码动手及设计上避免这类问题了。
## 发送 header 前不要有任何输出
发送或者修改 HTTP 头信息的方法必须在任何输出被输出之前被调用。否则调用将会出错：
>Warning: Cannot **modify** header information - headers already sent (**output** started at script:line)

这些方法可以修改（modify） HTTP 头信息：

* [header](http://php.net/header) / [header_remove](http://php.net/header_remove)
* [session_start](http://php.net/session_start) / [session_regenerate_id](http://php.net/session_regenerate_id)
* [setcookie](http://php.net/setcookie) / [setrawcookie](http://php.net/setrawcookie)

输出（output）可以是：

* 无意的：
    * `<?php` 之前或者 `?>` 之后的空格
    * [UTF-8 BOM](https://zh.wikipedia.org/wiki/位元組順序記號) 
* 有意的：
  * `print` ，`echo` 以及其他能产生输出的方法
  * 在 `<?php` 前原始的 `<html>` 区块

## 为什么这个错误会产生
为了理解为什么 HTTP header 必须在输出之前发送出去，我们有必要了解看一下一个典型的 HTTP 相应。PHP 脚本主要用来生成 HTML ，但它也会发送一系列的 HTTP/CGI 头信息到 web 服务器：

```html
HTTP/1.1 200 OK
Powered-By: PHP/5.3.7
Vary: Accept-Encoding
Content-Type: text/html; charset=utf-8

<html><head><title>PHP page output page</title></head>
<body><h1>Content</h1> <p>Some more output follows...</p>
and <a href="/"> <img src=internal-icon-delayed> </a>
```

页面或者输出总是紧跟在头信息后面。PHP 必须先把头信息发送给 web 服务器，并且它只能发送一次，在这之后就再也不能修改头信息了。

当 PHP 第一次接收到输出时（`print` ,`echo`,`<html>`） 它会清掉所有收集到的头信息。在此之后它能把输出所有想输出的内容，但是再想发送 HTTP 头信息就不可能了。

## 怎么找到到底是哪里提前产生了输出？
`header()` 头信息包含所有与问题产生相关的信息：

>Warning: Cannot modify header information - headers already sent by (output started at /www/usr2345/htdocs/auth.php:52) in /www/usr2345/htdocs/index.php on line 100

在上面的警告中，`line 100` 指向调用 `header()` 失败的脚本行数。

圆括号里的 `output started `  这条信息更加重要。它指出了先于 header() 前的输出的源头。在这个例子中是 `auth.php` 的 第 52 行，这就是你要去找的过早的输出的地方。

典型的原因有这些：

1. **print**,**echo**    
有意的 `print` 和 `echo` 语句输出将会中断输出 HTTP 头信息的机会。应用程序流必须重组以避免这种行为，可以使用 [function](http://php.net/function) 和模版来重组，从而保证 `header()` 调用是在信息被写出之前。    
产生输出的方法包括：    
 
 * print, echo, printf, vprintf
 * trigger_error, ob_flush, ob_end_flush, var_dump, print_r
 * readfile, passthru, flush, imagepng, imagejpeg    
 
 以及其他用户自定义的方法。

2. **原始的 HTML**
在一个 PHP 文件中未被解析的 HTML 区块也是输出。脚本中各种可能触发调用 `header()` 的条件都必须在任何 `<html>` 区块前声明。

3. **<?php 前的空格导致的 `"script.php line 1"` 警告**
如果警告指向第 **1** 行的输出，那么它很有可能指向的是在 `<?php` 之前的空格，文本或者 HTML 。    

    ```php
     <?php
    // 在 <?php 前有个空格 
    ```
  同样它可能出现在附加的脚本或者脚本区块上:

  ```php
  ?>

  <?php
  ```
  PHP 确实在闭合标签后占据了一个换行符，但是它不会在上面的空白处插入换行符、制表符或者空格（也就是说这是我们自己造成的）。    

4.  **UTF-8 BOM**
换行符或者空格可能导致问题，但是不可见的字符序列同样可以。最著名的就是大多数文本编辑器并不会显示的 **UTF-8 BOM** 。它是在 UTF-8 编码的文档里可选甚至是多余的，被标示为 `EF BB BF` 的字节序列。但是 PHP 必须把它当作原始的输出来处理。它可能以 `ï»¿` 这样的符号输出（如果客户端以 Latin-1 来解释这个文档）或者其他这样的“非法输出”。    
以某种图形化的编辑器或者基于 JAVA 的 IDE 查看这类文件时，你可能察觉不到 **UTF-8 BOM** 的存在。它们没有把 **UTF-8 BOM** 形象化（受制于 Unicode 标准）。然而大多数程序编辑器和控制台编辑程序会这样处理：
![](http://i.stack.imgur.com/aXgWY.png)    
像这样就能简单地提早发现问题了。其他的编辑器在设置某些选项后也能纠正这样的问题（Windows 上的 Notepad++ 可以识别并且 [纠正 BOM 问题](http://stackoverflow.com/questions/3589358/fix-utf8-bom) ），另一个发现 BOM 的方法就是借助十六进制的编辑器。在 \*nix 系统上，大都提供了 [`hexdump`](http://linux.die.net/man/1/hexdump)  ，如果没有的话，其他图形化的变种也可以用来简化审计这些问题的步骤：
![](http://i.stack.imgur.com/QyqUr.png)    
一个简单的修正方法就是将文本编辑器设置为  **以 UTF-8 (no BOM) 保存文件**（**save files as UTF-8 (no BOM)**）或者其他类似的设置。    
### 修正程序 
有很多自动化的工具可以检测并修改文本文件（[`sed`](http://stackoverflow.com/questions/1068650/using-awk-to-remove-the-byte-order-mark) / [`awk`](http://stackoverflow.com/questions/1068650/using-awk-to-remove-the-byte-order-mark) 或者 `recode` ）。PHP 里有 [`phptags`](http://freshcode.club/projects/phptags) 。它可以把打开标签和关闭标签重写成长标签（<?php）或者短标签（<?）的形式。也可以轻松地解决前导或尾随的空格、Unicode 和 UTF-x BOM 问题：    

  ```bash
  phptags  --whitespace  *.php
  ```
  同样，你可以在某个目录或整个项目目录使用这个命令。

5. **?> 后的空白**
 如果错误代码在闭合标签 >? 这一行的前面，那么这就是 >? 后的空格或者原始文本输出导致的问题。PHP 的结束标记并不会在遇到闭合标签时终止执行脚本，任何 ?> 之后的文本或者空格字符都会被当作页面内容输出。    
 通用的被鼓励的做法，特别是针对新手，是避免在 PHP 文件后加上闭合标签 **?>** 。这样就能避免一部分产生这类问题的情况。
 
6. **错误源提示："Unknown on line 0"**
如果没有给出具体的错误源，那么这就是典型的 PHP 扩展或者 `php.ini` 设置的问题：
  * 偶尔是 `gzip` 编码设置或者是 [`ob_gzhandler`](http://stackoverflow.com/questions/622192/php-warning-headers-already-sent-in-unknown)
  * 也有可能是 php.ini 设置里模块加载了两次导致 PHP 产生了启动/警告信息

7. **先前的错误导致输出了错误信息**
如果前面的 PHP 语句或者表达式造成了 warning 或者 notice 信息导致输出，这些输出也被认为是过早地输出。    
在这种情况下你需要避免错误，推后这些语句的执行，或者抑制这些信息的输出，可以使用 `isset()` 进行判断，或者使用抑制符 `@`，前提是它们不会阻止后续的调试。

## 没有错误信息输出
如果你禁用了 php.ini 里的 `error_reporting` 或者 `display_errors` 设置，那么将不会产生 warning 。但是忽略错误并不会让问题消失，头信息仍然不能在过早的输出输出之前发送出去。

所以当 `header("Location: ...")` 跳转静默地失败时，建议你去查看 warnings 。在脚本的最前面用下面的两条命令重新开启错误报告设置：

```INI
error_reporting(E_ALL);
ini_set("display_errors", 1);
```

或者如果其他的设置都失败了那就设置  `set_error_handler("var_dump");` 。

至于跳转的 header ，在执行至最后的代码时你应该遵循下面的这种风格：

```php
exit(header("Location: /finished.html"));
```

最好是提供一个方法，特别是当 `header()` 执行失败时打印出用户信息。

## 变通方法：输出缓冲
PHP 的输出缓冲的方法是缓解这种问题的一种变通方法。它运行起来可靠，但是你绝不要使用它来替代你架构良好应用程序结构，从控制逻辑中分离输出。它的真实目的是用来减轻大块数据传输至服务器时的压力。

1.  [`output_buffering 设置`](http://php.net/manual/en/outcontrol.configuration.php) 在 [php.ini](http://www.php.net/manual/en/configuration.file.php) 或者 [.htaccess](http://www.php.net/manual/en/configuration.changes.php) 或者甚至在最新的 FPM/FastCGI  的 [.user.ini](http://php.net/manual/en/configuration.file.per-user.php) 中设置；
2. 同样你可以在脚本的最前面使用 [`ob_start()`](http://php.net/ob_start) 来设置，但是它并没那么可靠：  
    * 即使 `<?php ob_start(); ?> ` 在第一个脚本里，空格或者 BOM 也有可能在此之前被输出
    * 它可以隐藏 HTML 输出里的空格（将空格放到 buffer 中），但是只要应用程序逻辑企图发送二进制内容（比如生成的图片），缓冲里的无关的输出就会成为问题（这样 ob_clean() 方法就成为下一步的变通方法了）。
    *  缓冲有大小限制，并且在默认配置下很容易超出，并且这种情况并不少见，一旦发生也不太容易追踪。

因此这两个方法变得不可靠了，特别是当你需要更改开发环境或者生产环境的配置的时候。这就是为什么输出缓冲被认为只是一种蹩脚的变通方法。

建议参考官方手册里的基本 [使用方法](http://www.php.net/manual/en/outcontrol.examples.basic.php) ，以及它的优缺点：

* [输出缓冲是什么](http://stackoverflow.com/questions/2832010/what-is-output-buffering)
* [为什么使用 output buffering](http://stackoverflow.com/questions/2148114/why-use-output-buffering-in-php)
* [使用输出缓冲是不好的实践吗？](http://stackoverflow.com/questions/4731375/is-using-output-buffering-considered-a-bad-practice)
* [正确使用 output buffering 解决 “header already sent” 的例子](http://stackoverflow.com/questions/2919569/use-case-for-output-buffering-as-the-correct-solution-to-headers-already-sent)

### **但是在其他的服务器上是好的？**
如果你之前没有收到过头信息的 warning ，那么 php.ini 里的 [output_buffering](http://php.net/manual/zh/outcontrol.configuration.php) 设置改变了。在现在的／不同的服务器上很有可能没有设置。

## **使用 `headers_sent()` 检查**
你可以使用 [`headers_sent`](http://php.net/headers_sent) 来检查是否可以发送头信息。这种方法可以有效地检查以便输出一个错误信息或是应用其他的逻辑。
不错的回退变通方法有：    

* **HTML`<meta>` tag**    
    如果你的应用程序很难在结构上解决这个问题，有个简单但显得不专业的做法是在 HTML <meta> 标签中来跳转网页。可以这样实现：    
    
    ```html
    <meta http-equiv="Location" content="http://example.com/">
    ```
    或者加上一个延迟时间
    
    ```html
    <meta http-equiv="Refresh" content="2; url=../target.html">
    ```
* JavaScript 跳转
    另一个可选的方法就是使用 [JavaScript 跳转](http://stackoverflow.com/questions/503093/how-can-i-make-a-redirect-page-in-jquery-javascript) 来实现网页跳转：
    
    ```javascripte
     <script> location.replace("target.html"); </script>
    ```
    
这种方式相比较 <meta> 方法起来更兼容 HTML 标准，它只依赖于可以运行 JavaScript 的客户端。
    
这两种方式在 HTTP header() 调用失败时都提供了可以接受的回退方式。理想化的处理方式应该是将跳转与其它方式结合，给出对用户友好的辅助信息并且提供一个可点的链接以供后续操作。

## **为什么 `setcookie()` 和 `session_start()` 都会被影响**
`setcookie()` 和 `session_start()` 都需要发送一个 `set-cookie:` 的 HTTP 头信息。这种情况就和前面输出 header() 的情况类似，所以同样会出现由于过早地输出错误信息导致的错误。

（当然它们受影响也有可能是因为客户端禁止了 cookie 导致的，设置可能是代理的问题。很明显，session 也取决去剩余磁盘空间大小或者 php.ini 里的其它设置）

---
原文链接：http://stackoverflow.com/questions/8028957/how-to-fix-headers-already-sent-error-in-php

