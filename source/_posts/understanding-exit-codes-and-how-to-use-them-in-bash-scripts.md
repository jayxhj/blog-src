---
title: 理解 Exit Code 并学会如何在 Bash 脚本中使用
date: 2016-02-27 18:51:02
tags:
 - bash
 - shell
 - exit code
categories:
 - 翻译文章
---

## Exit Codes 是什么
在 Unix 和 Linux 系统中，程序可以在执行终止后传递值给其父进程。这个值被称为退出码（exit code）或退出状态（exit status）。在 POSIX 系统中，惯例做法是当程序成功执行时传递 0 ，当程序执行失败时传递 1 或比 1 大的值。

传递状态码为何重要？如果你在命令行脚本上下文中查看状态码，答案显而易见。任何有用的脚本，它将不可避免地要么被其他脚本所使用，要么被 bash 单行脚本包裹所使用。特别是脚本被用来与自动化工具 SaltStack 或者监测工具 Nagios 配合使用。这些工具会执行脚本并检查它的状态，来确定脚本是否执行成功。

其中最重要的原因是，即使你不定义状态码，它仍然存在于你的脚本中。如果你不定义恰当的退出码，执行失败的脚本可能会返回成功的状态，这样会导致问题，问题大小取决于你的脚本做了什么。

## 如果不指定退出码会发生什么
在 Linux 里，任何在命令行中执行的脚本都有退出码。在 Bash 脚本中，如果脚本里没有指定退出码，退出码将会是脚本最后一个命令执行后产生的状态码。为了更好地解释退出码，下面将给出一个脚本来说明。

**脚本**

```bash
#!/bin/bash
touch /root/test
echo created file
```

上面的脚本既会执行 `touch` 命令也会执行 `echo` 命令。当我们以非 root 用户执行这个脚本时 touch 命令将会执行失败，理想情况下，当我们执行 touch 命令失败时我们希望通过脚本的退出码来表明有命令执行失败。我们可以通过打印 Bash 的特殊变量 `$?`  来获取退出码。这个变量将会打印出脚本最后一个命令执行的退出码。

**执行输出**

```bash
$ ./tmp.sh 
touch: cannot touch '/root/test': Permission denied
created file
$ echo $?
0
```

你可以看到，当执行 `./tmp.sh` 后退出码是 `0` ，而 0 代表脚本执行成功，虽然 touch 命令执行失败了。上面的脚本执行了两个命令：`touch`、`echo`。因为我们没有指定退出码所以脚本以最后一个命令执行后的状态码退出。在这个例子中，最后运行的是 `echo` 命令，这个命令确实执行成功了。

**脚本**

```bash
#!/bin/bash
touch /root/test
```

如果我们去掉脚本中的 `echo` 命令，我们将看到 `touch` 命令的退出码。

**执行结果**

```bash
$ ./tmp.sh 
touch: cannot touch '/root/test': Permission denied
$ echo $?
1
```

你可以看到，因为最后运行的命令是 `touch` ，所以脚本的退出码正确地反应了脚本的状态：执行失败。

## 在你的 Bash 脚本中使用退出码
当从我们的脚本中去掉 `echo` 命令后脚本跑通了并且返回了恰当的退出码。当我们想在 `touch` 命令执行成功执行一个操作，而在执行失败时执行另一个操作会发生什么。比如脚本执行成功时输出至 `stdout` ，执行失败时输出至 `stderr` 这种操作。

### 测试退出码
前面的代码中，我们使用了特殊变量 `$?` 来打印脚本的退出码。我们同样可以在脚本中使用它来测试 `touch` 命令是否执行成功。

**脚本**
```bash
#!/bin/bash
touch /root/test 2> /dev/null
if [ $? -eq 0 ]
then
  echo "Successfully created file"
else
  echo "Could not create file" >&2
fi
```

在上面的修改版代码中，如果 `touch` 的退出码是 `0` ，脚本将会输出成功的消息。如果退出码是除 0 以外的其他数字，这表示执行失败，脚本将会打印失败的消息到 `stderr` 。

```bash
$ ./tmp.sh
Could not create file
```

### 在程序中提供你自己的退出码
在上面的程序中，虽然 `touch` 命令执行失败时将会提供一个错误信息提示，但它仍给出了表示执行成功的状态码 0 。

```bash
$ ./tmp.sh
Could not create file
$ echo $?
0
```

既然脚本执行失败了，但它仍然传递执行成功的退出码给其他需要执行此脚本程序显然很不适合。为添加我们自己的退出码到这个程序里，我们可以简单地通过 `exit` 来实现。

**脚本**

```bash
#!/bin/bash

touch /root/test 2> /dev/null

if [ $? -eq 0 ]
then
  echo "Successfully created file"
  exit 0
else
  echo "Could not create file" >&2
  exit 1
fi
```

通过脚本里的 `exit` 命令，我们可以在 `touch` 命令执行成功时输出成功的消息并返回状态码 0 。当 `touch` 执行失败时我们将打印失败的消息至 `stderr` 并返回一个表示失败的状态码 1 。

**执行结果**

```bash
$ ./tmp.sh
Could not create file
$ echo $?
1
```

### 在命令行中使用退出码
现在我们的脚本既可以通知用户也能通知程序命令是否执行成功，我们可以使用这个脚本配合其他的管理工具，或者简单地通过 Bash 单行命令使用它。
**Bash One Liner:**
```bash
$ ./tmp.sh && echo "bam" || (sudo ./tmp.sh && echo "bam" || echo "fail")
Could not create file
Successfully created file
bam
```

上面的命令组使用了在 Bash 里称为 **list constructs** 的工具。它允许你通过 `&&`（代表 **and**） 和 `||` (代表 **or**） 将命令串到一起。上面的命令将会执行 `./tmp.sh` 脚本，如果退出码是 0 命令 `echo "bam"` 将被执行。但如果 `./tmp.sh` 的退出码为 1 ，圆括号里的命令将在之后被执行。圆括号里的命令通过 `&&` 、 `||` 被串到一起。

list constructs 使用退出码来知晓一个命令是否执行成功。如果脚本不恰当地使用退出码，其他使用更高阶的命令（比如 list constructs）调用此脚本的用户就可能得到与预期不符的结果。
## 更多关于退出码的信息
Bash 里的 `exit` 命令接受不了从 `0 - 255` 的整形数值，大多数情况下 `0` 和 `1` 就够用了，不过其他的数值也提供了，为更具体的错误信息做储备。The Linux Documentation Project 有一个不错的 [保留状态码](http://www.tldp.org/LDP/abs/html/exitcodes.html) 列表，告诉大家这些状态码作何使用。

---
原文链接：http://bencane.com/2014/09/02/understanding-exit-codes-and-how-to-use-them-in-bash-scripts/
