---
title: PHP5.2+Apache2.2的配置
tags:
  - PHP
id: 219
categories:
  - PHP
  - 编程学习与开发
date: 2013-08-16 01:33:48
---

由于安装PHP5.5的版本后在Apache的http.conf文件中配置php后导致Apache启动失败，错误提示为
`the requested operation has failed`

找官方文档发现配置php5模块的语句为 `LoadModule php5_module &quot;c:/php/php5apache2_2.dll;`，可是5.5的版本这个DLL文件后缀为2_4,修改后并没有成功，网上找大多建议安装VC运行库（[文章](http://blog.csdn.net/vancekq/article/details/7716555)）

其实问题归根结底是因为PHP版本与Apache版本不匹配，所以总结了下面的方法，很简单，结合了书上和官方文档的方法。

软件：PHP:php-5.2.17-Win32-VC6-x86.zip
Apache:httpd-2.2.25-win32-x86-openssl-0.9.8y.msi

步骤：安装Apache，按说明依次操作即可。解压PHP到某一目录。

在apache的http.conf文件中加入以下代码：
```apacheconf
LoadModule php5_module &quot;c:/php/php5apache2_2.dll;
AddHandler application/x-httpd-php .php
```
配置 php.ini 的路径
`PHPIniDir "C:/php;"`

在php.ini中设置

1、扩展dll目录
`extension_dir="c:/php/ext;"`

2、Web服务器根目录（Apache2.2）
`doc_root ="C:/Program Files/Apache Software Foundation/Apache2.2/htdocs;"`

3、打开运行的扩展
extension=php_mysqli.dll

以上配置中 **php** 解压到 `c:\php` **Apache** 安装在 `C:/Program Files/Apache Software Foundation/Apache2.2`
安装完成后重启 Apache，然后在 htdocs 放入 test.php 文件，内容为 `<? phpinfo();?>` 如若显示 PHP 的配置信息则安装成功。

有时间我在配置一下5.5版本的PHP再把安装过程贴出来。