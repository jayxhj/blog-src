---
title: 在 PHPStorm 中配置 Xdebug
date: 2016-07-01T16:04:05.000Z
tags:
  - Xdebug
  - PHP
categories:
  - PHP
---

在 [使用 Chrome 扩展程序 JSON Viewer 进行调试](/2016/01/using-json-viewer-for-debugging/) 这片文章中我曾介绍过，使用 `echo json_encode()` 的方式进行调试，再配合 Chrome 浏览器插件 ~~JSON Viewer~~ (建议使用 JSON Handler 代替)。

## 调试方式的对比

手动调试的方式实际上局限性很大，缺点很明显：

1. 复杂的程序变量的中间状态无法跟踪，只能得到最终结果
2. 要 debug 的变量或者对象较多时不方便打印
3. 调试代码与程序代码混杂在一起，容易出错，而且来回切换成本还挺高，效率上就低多了

上面的缺点中，

1 可以通过 Xdebug 的单步调试解决，通过打断点，一步步追踪，可以深入某个 function 或者一步步执行，来查看变量的整个状态变化。

2 这个缺点可以通过 `error_log()` 函数写入文件中，再配合 `tail -f log_file` 来调试，当然在 Xdebug 中也能一步步查看变量状态。

3 也是我决定用 Xdebug 的原因，打断点很方便管理，不用的断点暂时反选，这样可以在需要启用时启用，而且无需写调试代码，这种调试方式是非侵入式的。

## 开发中的痛点及思考

以上是我在开发过程中遇到的痛点，而解决方法很简单：

1. 类与类之间低耦合，这样可以将 bug 缩小范围，且代码也更健壮，方便后续修改
2. [SOLID 原则](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)
3. 引入单元测试覆盖大部分的功能及类的测试

其中 2 是需要在类的设计上下功夫的，需要长时间的代码编写与逐步改善，1 则可以使用设计模式来解决，3 则可以让你将注意力放在各个功能的衔接点上，通过上面的三个方法，基本能解决平常的小 bug 了。

下面来介绍 debug 工具 Xdebug ，并介绍如何在 PHPStorm 中配置使用。

## Xdebug 的安装与配置

Xdebug 有很多安装的方式，这个页面 <https://xdebug.org/docs/install> 给出了常用的安装方式。 另外有个更好的页面，给出了一步步的步骤，<https://xdebug.org/wizard.php> ，会告诉你如何编译安装指定的版本。

安装步骤如下：

1. 下载对应的 Xdebug 压缩包；
2. 编译

  ```bash
  cd /path/to/xdebug
  phpize
  ./configure
  make
  ```

3. 复制 so 文件到扩展目录

4. 修改 php.ini 加上以下配置

  ```ini
  [Xdebug]
  zend_extension = /path/to/extensions/xdebug.so
  xdebug.remote_enable=1
  xdebug.profiler_enable=1
  xdebug.remote_port=9000
  xdebug.profiler_output_dir= /tmp/xdebug
  ```

> phpize 命令是用来准备 PHP 扩展库的编译环境的。

使用 `php -m` 查看扩展是否加载，再重启 php-fpm 。

下面介绍如何在 PHPStorm 中集成 Xdebug 。

## PHPStorm 里配置 Xdebug

PHPStorm 文档里介绍了如何配置 Xdebug <https://www.jetbrains.com/help/phpstorm/10.0/configuring-xdebug.html>

介绍下几个主要的步骤：

1. 设置 PHP 解释器。位于 Preferences > Languages & Frameworks > PHP 右侧的 Interpreter ，点击 ... 按钮，设置本地的 PHP 环境
2. 配置 Xdebug 的行为。

  1. 配置 **Debug Port** ，让其与 php.ini 中的 `xdebug.remote_port`端口号相同。
  2. 接收 Xdebug 与 PHPStorm 的连接，**Can accept external connections**

3. 当程序运行至端点处时，自动停止，开启这个选项 **Run | Break at first line in PHP scripts**

通过上面的配置，就可以在 PHPStorm 运行 Xdebug 了。不过这还不够，需要配置具体的项目，这样 Xdebug 才知道如何运行，并在指定的断点处停止并通知 PHPStorm 。

## 配置实例运行具体的 debug 项目

下图中显示了如何配置某个 debug 项目。

![debug configuration](http://img.jayxhj.com/add-debug-configuration.png)

选择 **PHP Web Application** 即可配置某个 web 项目，设置项目的 url 及当前配置的名称即可。

以上这些步骤走完，一个 web 项目的 Xdebug 配置就做好了，只需设置断点，再点击 Run -> Debug '你的 debug 项目名称' ，即可开始 debug 之旅了。

Enjoy it.
