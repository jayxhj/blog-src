---
title: 使用 Chrome 扩展程序 JSON Viewer 进行调试
id: 423
categories:
  - PHP
date: 2016-01-23 19:05:50
tags:
---

PHP 调试时有很多种方法，其中最简单的无非是 echo 与 var_dump 、 print_r 以及 debug_backtrace 了。这篇文章要介绍的是平常用的比较多的，适合在调 HTTP 接口以及平常开发中需要输出变量内容的情况。其实在 IDE 中比较适合使用 XDebug 或者断点调试，这样能在运行时直接查看当前上下文的各个变量，不过这种情况不在此篇讨论之列，后续我也将介绍相关的方法，来完善这个系列。

现在步入正题。在平常和前端的配合中经常会有一些 RESTful 接口输出，而其中最常用的内容传输格式就是 JSON ，JSON 在 PHP 中可以很方便地转化为对象或者数组，在打印变量时使用 JSON 再配合浏览器的扩展进行解析，无疑能提高开发效率。

# JSON

先用两张图来了解一下 JSON 的格式，这样就不会混淆其在 PHP 中所代表的意思。

> 对象是一个无序的“‘名称/值’对”集合。一个对象以“{”（左括号）开始，“}”（右括号）结束。每个“名称”后跟一个“:”（冒号）；“‘名称/值’ 对”之间使用“,”（逗号）分隔。

![](http://www.json.org/object.gif)

> 数组是值（value）的有序集合。一个数组以“[”（左中括号）开始，“]”（右中括号）结束。值之间使用“,”（逗号）分隔。

![](http://www.json.org/array.gif)

> 值（value）可以是双引号括起来的字符串（string）、数值(number)、true、false、 null、对象（object）或者数组（array）。这些结构可以嵌套。

![](http://www.json.org/value.gif)

# Chrome 扩展

JSON viewer 扩展的地址是 [https://chrome.google.com/webstore/detail/json-viewer/gbmdgpbipfallnflgajpaliibnhdgobh](https://chrome.google.com/webstore/detail/json-viewer/gbmdgpbipfallnflgajpaliibnhdgobh) 

JSON view GitHub 官网介绍了其有如下 feature

*   语法高亮
*   23 个内置的主题
*   节点可折叠
*   可点击的 url
*   在控制台「console」输入 json 回车中查看你的 JSON （通过访问 id 为 json 的 HTML 元素）
*   显示行号选项
*   在 url 中加入时间戳 header

等等。

# PHP 配合 JSON viewer 进行输出调试

PHP 中用来处理 JSON 使用最多的是 json_encode 和 json_decode ，比如你有 array 有如下结构
```php
$arr = array(
            'key1'  => 1,
            1       => 'string',
            'array' => array(
                'value1',
                'value2',
            )
        );
```

如果使用 var_dump 输出，那么长的是这样 
```php
Array
(
    [key1] => 1
    [1] => string
    [array] => Array
        (
            [0] => value1
            [1] => value2
        )

)
```

当层级不够多，数组元素不够多，肉眼观察根本不是问题，但是一旦数据变得复杂，输出将会非常难看且不易查找，这时候一个美观的输出外带查看层级和数据复制功能会显得很方便，此时，你只需要 `echo json_encode($arr);exit;` ，扩展会自动解析 JSON 。

最终你得到的可能是这样的 UI：

![](https://raw.githubusercontent.com/tulios/json-viewer/master/screenshot.png)

好了，其实还有很多输出调试工具，比如 symfony 的 [var-dumper](https://github.com/symfony/var-dumper) 既能输出 HTML 的调试信息，又能在 cli 下输出带格式的调试信息。还有debug helper [Kint](https://github.com/raveren/kint/) 、Yii 的 [yii-debug-toolbar](https://github.com/malyshev/yii-debug-toolbar) 以及强大的 XDebug 。

* * *