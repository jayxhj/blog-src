---
title: 32位Windows7下PHP5.5与Apache2.4的集成
tags:
  - Apache
  - PHP
  - PHP5.5
id: 287
categories:
  - PHP
date: 2013-09-18 10:20:58
---

PHP官网提示：如果你的 Apache 是在 apache.org 上下载的，那么你必须使用旧的VC6编译版本的PHP，不能使用VC9 + apache.org二进制版本的PHP(意思就是如果想安装 PHP5.5 必须使用[http://www.apachelounge.com/download/](http://www.apachelounge.com/download/) 网站的 Apache 安装包)。

如果想安装更高版本的PHP，那么需要下载 VC11 builds of Apache for x86 and x64。下载 5.5 版本的 PHP，最好下载线程安全的版本。下面介绍安装步骤。

1. 配置Apache2.4：解压下载的apache2.4，在附件中找到命令提示符，以管理员身份运行，切换到apache的bin目录，命令为cd  c:/Apache24/bin ，安装apache24服务，命令为httpd.exe -k install ，这样点击目录里的ApacheMonitor.exe就可以开启apche了，在浏览器输入127.0.0.1可测试，若出现**It Works！**表示apache配置成功。

2. 配置PHP5.5 :将下载的PHP5.5的文件解压到某一目录，将目录里的php.ini-production文件另存为php.ini，修改其中的doc_dir, 如doc_root ="C:/Apache24/htdocs"，extension_dir = "d:/php/ext"

3. 修改Apache的httpd.conf文件，添加如下语句
```apacheconf
LoadModule php5_module "c:/php/php5apache2.dll"
AddHandler application/x-httpd-php .php
```
configure the path to php.ini
`PHPIniDir "C:/php"`
这样当处理的网页为.php后缀时将交给php引擎进行处理。

4. 测试：在htdocs目录中添加test文件，文件内容为< ?php phpinfo();?>,若出现php的配置信息则代表php与apache配置成功。
注意：由于php5.5由VC11编译完成，需要Visual C++ 运行库的支持，下载地址为x86 or x64。

链接汇总：

1. Apache2.4：[http://www.apachelounge.com/download/](http://www.apachelounge.com/download/
2. PHP5.5：[http://windows.php.net/download/#php-5.5](http://windows.php.net/download/#php-5.5)  建议下载Thread-safe版本
3. Visual C++ 运行库[http://www.microsoft.com/en-us/download/details.aspx?id=30679](hthttp://www.microsoft.com/en-us/download/details.aspx?id=30679) 如果网站加载缓慢，建议直接百度Visual C++ 2012运行库下载即可。