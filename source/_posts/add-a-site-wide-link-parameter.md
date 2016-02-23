---
title: 为网站全站链接添加参数
id: 376
categories:
  - 技术实现
date: 2014-07-19 16:40:06
tags:
---

### 需求
为网站链接添加参数，可以统计用户在网站中的流向，以及确定网站经常访问的部分。

### 解决方案
 1. 在程序生成链接的时候附带转向，这样需要改变所有生成链接的地方
 2. 使用jQuery在网站加载时动态改变网站页面内所有链接

### 利弊
使用第一种方案时当需要改变某一页面的来源时，所有与本页面有关的链接都需要改动，好处是页面输出到浏览器后即为用户最终所得，不存在链接参数错误的问题。
使用第二种方案好处是修改页面链接时只需在页面标签的父级标签添加某一属性（如例子给出的from），即可定位相当精准，但弊端在于此方法必须在页面DOM树加载完成之后执行，假如中途DOM树出问题或者标签属性输出不全可能导致链接错误，另外当标签内的链接由 JavaScript 单独进行定义时需额外修改，这是两种方案都要面对的问题。

### 使用jQuery实现的版本
####思路：
1. 查找所有a节点
2. 获取其父类，一级一级往上找，使用closest()查找，获取其from参数值
3. 判断a节点的href属性是否包含?，包含则直接添加 &from ，否则添加?&from。如果添加href为javascript:或者 javascript:void(0) 时则不改变其href属性
前提：链接不是javascript: 且链接父级标签含有属性from
如网页链接为www.jayxhj.com
标签层级为
`<p from="top"> <a href="/test"></a> </p>`

则执行addparam()之后变为

`<p from="top"> <a href="/test?f=top"></a> </p>`

样例:www.baidu.com转换后为 www.baidu.com?f=top 
www.baidu.com?r=test转换后为 www.baidu.com?r=test&f=top
```javascript
function addparam() {
    $("a").each(function () {
        var realhref = $.trim($(this).attr('href'));
        var href = '';
        if (realhref.indexOf('?') === -1) {
            href = realhref;
        } else {
            if (realhref.indexOf('&') === -1) {
                if (realhref.indexOf('f=') === -1) {
                    href = realhref;
                } else {
                    var hrefarr = realhref.split('&');
                    var length = hrefarr.length - 1;
                    for (var i = 0; i < length; i++) {
                        href += hrefarr[i] + '&';
                    }
                    href = href.substring(0, href.length - 1);
                }
            } else {
                var hrefarr = realhref.split('&');
                if (realhref.indexOf('f=') === -1) {
                    href = realhref;
                } else {
                    var length = hrefarr.length - 1;
                    for (var i = 0; i < length; i++) {
                        href += hrefarr[i] + '&';
                    }
                    href = href.substring(0, href.length - 1);
                }
            }
        }
        if (!~href.indexOf('javascript:') && href !== '') {
            var parent = $(this).parents();
            for (var j = 0; j <= parent.length; j++) {
                if (typeof ($(parent[j]).attr('from')) != 'undefined') {
                    $(this).attr('href', href + (href.indexOf('?') === -1 ? '?' : '&' ) + 'f=' + $(parent[j]).attr('from'));
                    break;
                }
            }
        }
    });
}
```
具体可参看[股票雷达网站](http://www.gu360.com/)