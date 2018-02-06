---
title: FaceBook 命令行 UI PathPicker
id: 415
categories:
  - 工具技巧
date: 2016-01-17T17:40:38.000Z
tags: null
---

早先在逛 Facebook 后端开源项目时看到了一个命令行工具 PathPicker ，安装后试用了一下感觉还不错，恰好项目开发时在使用 Git ，发现结合 PathPicker 后免去了很多麻烦。

试想以下场景：

1. 你完成一个功能会涉及到不少文件，你得小心翼翼地一个个文件选择，防止不必要的文件出现在同一个待提交列表中；

2. 你改了很多文件，然后执行了 `git add .` ，这时你有两个选择，要么 `git rest` 不想在这次提交中包含的文件，要么一个个选择，然后 添加到 `git commit` 的后面。

于是 PathPicker 出现了，专为解决此种费力不讨好的事情，UI 选择器，命令行，一起上。

## 介绍

FaceBook PathPicker 是一个简单的命令行工具，用来处理选择 bash 文件输出时的问题。

PathPicker 做以下事情：

- 通过管道处理输入中所有表现出文本特性的文件
- 在一个方便选择的 UI 容器里展现管道输入
- 然后你可以对输入做以下事情：

  - 用你最喜欢的编辑器处理选择的所有文件
  - 利用输入执行任意命令

下面通过一段视频来了解 PathPicker：

<script type="text/javascript" src="https://asciinema.org/a/19519.js" id="asciicast-19519" async="">
</script>

## PathPicker 是怎么运行的

PathPicker 是 bash 脚本和一些 Python 模块 结合后的产物。 它主要有以下三个步骤：

1. 首先在 bash 脚本中，它将所有的标准输入重定向到一个 Python 模块，然后通过解析取出其中所包含的文件名候选列表。每个候选文件名都将被用来与文件系统做比对确保其存在，再然后，会把处理结果保存至临时文件并终止 python 脚本的运行。
2. 第二部，bash 脚本会切换至命令行输入模式，并且另一个 python 模块会读取出已保存的条目并使用 `curses` 在一个 UI 选择器展现它们。
3. 最后，python 脚本会输出一条命令至 bash 文件，最终被原始的 bash 脚本解析执行。

## 使用示例

当改了很多文件需要提交提交时，查看当前状态 `git status`

![git add before](//blog.jayxhj.com/images/PathPicker-git-add-before.jpg)

将输出重定向至 PathPicker `git stataus | fpp`

![git status|fpp](//blog.jayxhj.com/images/PathPicker-git-statusfpp-after.png)

- 已选中为绿色
- 已选中且当前光标悬停显示为红色
- 未选中且当前光标悬停显示为蓝色
- 无法选中的光标无法跳到此项，显示为纯文本

提交选定的文件 `git commit $F`

![git commit $F](//blog.jayxhj.com/images/PathPicker-git-commit-F.png)

PathPicker 官网见 <http://facebook.github.io/PathPicker/>

--------------------------------------------------------------------------------
