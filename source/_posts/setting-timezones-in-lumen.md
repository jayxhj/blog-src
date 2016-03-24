---
title: 在 lumen 中设置日期与数据库时区
date: 2016-03-24 19:17:10
tags:
  - php
  - laravel
  - lumen
  - timezone
categories:
  - PHP
  
---

项目使用了 Laravel 的精简版 Lumen 来做 api 和 sso 的服务。今天在提交代码时发现，生成的 database migration 代码时间相差八个小时。看来是时区设置的问题，查找了一下，发现有好几个地方都有时区的设置，那就简单总结一下。

## PHP 时区设置
PHP 里可以在 php.ini 中设置 **`date.timezone`** 选项来设置时区，也可以在脚本中动态制定时区，如使用 **`date_default_timezone_set()`** 来动态指定时区。使用脚本指定时会覆盖 php.ini 中的设置。时区列表可以使用 **`\DateTimeZone::listIdentifiers()`** 来获取。也可以到 [timezonemap.h](http://lxr.php.net/xref/PHP_5_6/ext/date/lib/timezonemap.h) 来查看对应的可选项。

中国的时区设置可以使用以下选项：
```C
{ "cst",   0,  28800, "Asia/Chongqing"                },
{ "cst",   0,  28800, "Asia/Chungking"                },
{ "cst",   0,  28800, "Asia/Harbin"                   },
{ "cst",   0,  28800, "Asia/Macao"                    },
{ "cst",   0,  28800, "Asia/Macau"                    },
{ "cst",   0,  28800, "Asia/Shanghai"                 },
{ "cst",   0,  28800, "Asia/Taipei"                   },
{ "cst",   0,  28800, "PRC"                           },
{ "cst",   0,  28800, "ROC"                           },
```

## lumen 时区设置
lumen 的时区设置有数据库操作的时区设置以及与使用时间相关的函数的时区设置。

### 日期相关设置
在 lumen 中应用程序的入口，会 new 一个 Application 实例，这个实例会读取 env 配置文件中的 **APP_TIMEZONE** 配置，若没有配置则会使用 UTC 时间。Application 位于 

```php
/path/to/project/vendor/laravel/lumen-framework/src/Application.php
```

所以最好是在 .env 中设置时区 `APP_TIMEZONE=PRC` 。

### 数据库
数据库的时区设置可以在 config 的 database 文件中设置。database 文件分两个，一个是框架级别，一个是项目级别。框架级别的文件位于 

```php
/path/to/project/vendor/laravel/lumen-framework/config/database.php
```

而项目级别的文件位于 
 ```php
 /path/to/project/config/database.php
 ```
 当然建议是在项目级别设置时区。在 .env 中指定 `DB_TIMEZONE=PRC` 和在 database 中指定 `'timezone'  => env('DB_TIMEZONE', '+08:00')` 均可。

## 总结
时区设置无非就是 PHP 配置以及运行时配置，而在 lumen 中还涉及到环境变量的配置。lumen 中既可以在 config 目录下的文件中指定，也可以在 .env 文件中指定。建议既指定 PHP 的配置又指定环境变量的配置，环境变量的配置可供整个应用程序使用，是项目级别的共享配置。
