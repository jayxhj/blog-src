---
title: 调试线上 JS 的几种姿势
date: 2016-12-15T18:16:20.000Z
tags:
  - JS
  - debugging
---

线上某个页面出问题时，我们需要查看日志，需要看页面表现，需要定位问题在哪里以便及时修复。一般后端会通过打日志的形式来确定 bug 的起源，前端就不一样了，上线前 JS 经过混淆、压缩、合并，代码已经不是原来的样子了，无疑给调试带来了困难。那么如何定位问题呢？本文希望从两方面解释，一方面是如何去复现问题，第二步是根据第一步的信息，如何更快更科学地 debug 。

# 问题的定位

## 本地复现、调试、解决、上线

有些问题其实不用在线上调试，如果场景容易满足，case 容易在线下构造，那么直接在本地改就好了，这种方式比较适合简单的问题，无需特定场景和环境，bug 就能修复。

## 使用 Chrome 浏览器进行 debug

调试页面的问题，使用最多的浏览器就是 Chrome 和 Firefox ，而我习惯于使用 Chrome ，就介绍一下使用 Chrome debug JS 的姿势。

Google 官方给出了 [DevTools](https://developers.google.com/web/tools/chrome-devtools/) 的介绍，里面有很多案例介绍及使用方法介绍，以博客的形式给出，很方便。

下面介绍的内容实际上就是从 [Set Breakpoints](https://developers.google.com/web/tools/chrome-devtools/javascript/add-breakpoints) 这个小节学习的。摘录某个调试方法。

### 设置 DOM 改变时的断点

你代码的某个地方出现了 bug ，它错误地改变、删除或者增加一个 DOM 点。DevTools 提供了一个快速找到 bug 根源的工具：针对 DOM 改变的断点。

DevTools 让你可以在某个节点上打断点，让你不用手动地在代码里四处寻找是哪里导致了 DOM 的变化。无论在哪个节点，或是节点的子节点，当它们背添加、删除、改变时，DevTools 都能让页面暂停，然后把你带到引起 DOM 改变的具体代码行。

添加 DOM 改变的断点 的方式： 右键单击要监控的节点，选择要监控的行为，当节点发生改变时，JS 将停止执行，页面将显示引起监控的 DOM 节点改变的 JS 代码行。另外，从 Call Stack 可以查看引发当前监控 DOM 发生改变的 JS 调用栈。

监控节点断点类型：

以下是各个类型的 DOM 节点改变时的断点详细说明：

- **Subtree modifications** .

  当当前选择的节点的子节点被删除、添加、或者内容发生变化时触发。当子节点的属性改变或者当前节点发生改变时不会触发。

- **Attributes modifications**.

  当当前节点的属性被添加或者删除，或者当前节点的某个属性的值发生变化时触发。

- **Node Removal**.

  当当前节点被删除时触发。

设置 DOM change 相关的断点最大的好处在于，能准确定位到是哪里引起了改变，缺点在于，当出现不对 DOM 产生改变的 bug 时，这种方法就失效了。这种情况，就得确定 bug 的代码范围，再使用断点逐步追踪。

# JS 的调试

众所周知，静态文件会存在缓存问题。一个页面的加载，如果包含多个 JS ，如何定位到具体的 JS 以及如何使用修改后的 JS 来查看页面效果就很是问题了。下面介绍的方式可以解决前面的问题，原理都是使用本地的 JS 做代理，来接管线上的 JS ，这样线上的调试就转移到本地了。由于线上的 JS 一般都经过压缩、混淆、合并，故最好本地有合成线上 JS 的子 JS ，下面介绍的方式是直接用本地的 JS 替代线上的 JS ，如果是前面说的那种情况，用合并后的 JS 替换线上 JS 即可。

## 使用代理软件

macOS 系统下，Web 调试代理软件一般使用 [Charles](https://www.charlesproxy.com/) ,使用代理软件的好处是，网络请求的所有资源，请求的细节，都能记录下来，另外它能作为系统代理接管浏览器的网络访问，将这些请求记录下来，是 Web debugging 的利器。

下面要介绍的方式就是 Charles 的代理功能，使用本地的 JS 代理线上的 JS 。

1. 设置 Charles 为系统代理，代理 Chrome 的网络连接。

  这个步骤，需要开启 Charles ，并设置 Charles 为系统代理。Firefox 可以直接设置，Chrome 网络设置实际上就是修改的系统的网络设置，步骤为 **系统偏好设置 -> 网络 -> 高级 -> 代理** 一图胜千言 ![系统代理](//blog.jayxhj.com/images/ServeAsSystemProxy.jpeg)

2. Map Local

  使用 Chrome 请求网页， 即可在 Charles 中查看到当前网页所有资源对应的请求，将某个 JS 文件映射到要用作代理的本地 JS 文件，或者粗暴点，直接代理目录，就可以代理文件夹下的所有 JS 了。

  ![map local](//blog.jayxhj.com/images/MapLocal.jpeg)

这个方式下，当在本地修改 JS 后，能直接在线上环境看到效果，最重要的是，免去了修改 JS 后上线 JS 不生效的问题。归根结底，本地修改，上线再看效果这种原始的方式，本该淘汰。

但此方式有个缺点，需要来回切换，你需要切换到编辑器或者 IDE 改好后再回 Chrome 刷新页面看效果，而且不能实时编辑，下面的使用 **Chrome workspace** 的方式无疑是效果最好而且最省时省力的。

## 使用 [Chrome workspace](https://developers.google.com/web/tools/setup/setup-workflow)

此方式实际上也是使用本地的文件做代理，但区别于其他方式的是，它与 Chrome 无缝集成，是 Chrome DevTools 提供的功能，支持断点，支持将修改持久化到本地。下面介绍配置方式。

1. 将本地文件添加到 workspace

  - 开启调试面板，切换至 Sources 选项卡
  - 右击选择 **Add Folder to Workspace** ![Add Folder to Workspace](//blog.jayxhj.com/images/addfolder.png)
  - 选择要代理的本地的文件夹
  - 点击地址栏下方弹出来的窗口，选择 **允许** ，Chrome 才能获得访问本地文件的权限

2. Map to File System Resource

  选择要代理的文件，Chrome 将自动列出可以映射的文件，此时就建立了远程文件与本地文件的映射。 ![Map to File System Resource](//blog.jayxhj.com/images/maptoresource.png)

设置好之后当 debug 好之后，可以直接将修改持久化到本地的文件，再也不用复制粘贴了。

其他用法见官方介绍：<https://developers.google.com/web/tools/setup/setup-workflow>
