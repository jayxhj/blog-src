---
title: Git 常用操作
id: 408
categories:
  - 工具技巧
date: 2015-12-10T20:15:55.000Z
tags: Git
---

Git 常用操作

--------------------------------------------------------------------------------

# Git 常用操作

## Git 忽略已追踪文件的修改

`git update-index –assume-unchanged /path/to/file`

## Git 取消忽略文件的修改(再次追踪此文件)

`git update-index --no-assume-unchanged /path/to/file`

## 导入 Git 仓库至 Bitbucket

1. create repository in [Bitbucket](https://bitbucket.org/repo/create)
2. do upstreaming in local

```bash
cd /path/to/my/repo
git remote add origin https://username@bitbucket.org/username/reponame.git
git push -u origin --all # pushes up the repo and its refs for the first time
git push -u origin --tags # pushes up any tags`
```

## 分支重命名

1. 修改当前分支 `git branch -m <newname>`
2. 未提交至 remote  `git branch -m <oldname> <newname>`
3. 已提交至 remote 需新加分支 `git push origin <newname>:<newname>` 并删除旧的远程分支 `git push origin  :<newname>`
