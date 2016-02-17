---
title: Git 常用操作
id: 408
categories:
  - 工具技巧
date: 2015-12-10 20:15:55
tags:
---

<div id="MathJax_Message" style="display: none;"></div>

Git 常用操作

* * *

1.  Git 忽略已追踪文件的修改
<pre>git update-index –assume-unchanged /path/to/file</pre>
Git 取消忽略文件的修改(再次追踪此文件)
<pre>git update-index --no-assume-unchanged /path/to/file</pre>
2.  导入 Git 仓库至 Bitbucket

    1.  create repository in [Bitbucket](https://bitbucket.org/repo/create)
    2.  do upstreaming in local
<pre>cd /path/to/my/repo
git remote add origin https://username@bitbucket.org/username/reponame.git
git push -u origin --all # pushes up the repo and its refs for the first time
git push -u origin --tags # pushes up any tags</pre>