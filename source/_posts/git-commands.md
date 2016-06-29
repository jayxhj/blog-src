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
2. 未提交至 remote `git branch -m <oldname> <newname>`
3. 已提交至 remote 需新加分支 `git push origin <newname>:<newname>` 并删除旧的远程分支 `git push origin :<newname>`

## 从某个分支上线

场景：当需要从某个分支的指定版本上线，又需要保留当前分支的修改

1. checkout 到指定分支 `git checkout [revision]` [revision] 代表指定 commit 的 hash
2. 以当前 commit 创建分支 `git checkout -b [branchName]` [branchName] 为分支名
3. 推送至远程 `git push origin branchName:branchName`

## 添加多个远程分支

```bash
git remote set-url origin --push --add <a remote>
git remote set-url origin --push --add <another remote>
```

## 错误 merge 了某个分支后撤销

场景：当某个功能开发并自测完毕，需要 merge 到测试环境进行测试时，如果不小心 merge 错了分支，这时候需要撤销这个 merge 。如果是使用 Pull Request 进行分支合并的话，直接 close 就可以了，但如果是使用命令行进行操作的话，本地的分支实际上已经与待 merge 的分支进行合并了，需要回到 merge 前。

只需要两个一个操作：找到 merge 前的 commit hash ，并 reset --hard

```bash
>git reset --hard <commit hash>
```
