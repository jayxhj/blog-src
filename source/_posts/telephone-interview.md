---
title: 第一次电话面试
tags:
  - PHP
  - 求职
  - 面试
id: 227
categories:
  - PHP
date: 2013-09-16 22:31:08
---

这是第一次在网上投简历有结果，上午十一点对方公司打电话过来说下午两点安排技术人员和我电话面试，下午两点一到对方电话就打过来了，效率很高很守时，我非常喜欢。
下面的是面试的内容，最大的感触就是基础得练扎实，得多实践。

自我介绍
### PHP
常见的PHP字符串处理函数？
Session和cookie的区别？
Session 储存于服务器端， cookie 储存于客户端。分别使用超全局变量 $_SESSION 和 $_COOKIE 数组访问。
Require()和include()的区别？
require() 意为”必须”，include() 意为”包含”。它们都能实现包含一个文件的功能，但
include() 语句如果指定的文件无法定位，代码继续运行，而 require() 语句会导致代码停止运行并抛出错误。若文件找不到，两者都会报错但是只有 require() 语句会完全终止代码得运行。require_once() 语句与 include_once() 语句保证被包含的文件只被引入一次，防止重复定义和执行的问题。

## HTTP
常见的HTTP状态码及表示的意思？

## 数据库
联结语句有哪些有什么区别？
内联结和外联结与交叉联结，外联结分为左外联结和右外联结。左外联结以左表为主表，右外联结以右表为主表。
性能优化，举例?
一张表有100条记录（表a），另一张有60条记录（表b），左联结后记录为多少，右联结后记录数为多少？
左联结时语句为 
`SELECT *
FROM a LEFT OUTER JOIN b
ON a.col1=b.col1`
返回结果有100条，其中记录行中所有不满足条件的记录，表b的那几列均为NULL
右联结语句
`SELECT *
FROM a RIGHT OUTER JOIN b
ON a.col1=b.col1`
返回结果有60条，其中记录行中所有不满足条件的记录，表a的那几列均为NULL

## 算法
常见的排序算法，时间复杂度为多少，空间复杂度为多少？
世界上最快的排序算法是什么？

上面我能回答的都在这里，待我能回答了再到评论里进行更新。
最大的感触就是学的确实不够扎实，面试官建议好好练基础，然后找项目时间。在压力下你会逼着自己把事情做出来。
很高兴，虽然今天感觉被打击了，但也在意料之中也在情理之中。
好好学习吧，努力会有收获的。