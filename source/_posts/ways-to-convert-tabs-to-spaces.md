---
title: 将文件内的 tab 转化为空格
date: 2016-08-20T18:15:46.000Z
tags:
  - vim
  - bash
  - tab
---

tab 与 space 孰好孰坏这个问题是程序界的圣战之一。StackExchange 旗下的 programmers 就有关于这个问题的 [讨论](http://programmers.stackexchange.com/questions/57/tabs-versus-spaces-what-is-the-proper-indentation-character-for-everything-in-e) ,没有必要大打出手，倒是可以了解下各种情况下使用这两者的差异及各自的优势。不过本篇不准备讨论这个问题，本篇只想解决日常开发中的需求：将 tab 转化为空格。

总结了一下，大概有以下几种思路：

1. 通过程序进行替换
2. 通过重定向输入替换，生成新文件

第一个思路适合大批量的替换，也适合大文件的替换，第二个思路实际上是读取文件替换后生成新文件，和第一种差别其实不大，但是很多软件支持指定参数，就非常适合通过管道及重定向来进行替换了。

## 通过 vim

### 通过配置去格式化

在 ~/.vimrc 中加入以下配置

```text
set tabstop=4
set shiftwidth=4
set expandtab
```

打开要格式化的文件，运行 `:retab` 即可格式化。

或者一行搞定：`:set tabstop=4 shiftwidth=4 expandtab | :retab`

这种配置方式对所有的文件都生效，如果需要添加例外，添加下面的配置即可 `autocmd FileType make setlocal noexpandtab` FileType 后接文件类型名称。

这种方式是通过配置的方式来格式化文本，定制性比较高。

### 通过正则替换

打开文件，直接运行下面的命令即可：`:%s/\t/ /g` 。

## 通过 expand

由于 expand 是通过读取文件或标准输入并重定向至标准输出，故要达到更改的效果，需重新写入原文件。

```bash
expand -t 4 input > output #间隔默认为 8
```

## 通过 col

原理同 expand ：

```bash
cat -vt tab_file | col -x > output
```

## 通过 sed

大杀器登场，通过 sed 来处理文本替换这种事情无疑是科学有效又简洁的姿势。

```bash
sed -iE 's/\t/    /g' file #从头换到底
```

--------------------------------------------------------------------------------

不过在我看来，为什么这么做，比怎么做到更有价值，网上的讨论还是很多的，在我看来最主要的是习惯问题，以及涉及到团队合作时，统一格式及风格，再就是涉及到文件交换及接口约定时，使用符合特定领域约定俗成的规范。基本就是这样的原则。

看看西乔给的问卷及统计结果 [缩进圣战的统计结果](http://mp.weixin.qq.com/s?__biz=MzAxMzMxNDIyOA==&mid=216031001&idx=2&sn=977df68e90ebce478fe0c14ffc33530b&scene=1&srcid=08209I9tmAKiZMwzhAfEIwUx#rd) 。
