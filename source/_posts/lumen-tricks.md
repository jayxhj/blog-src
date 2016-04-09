---
title: Lumen 常用开发技巧
date: 2016-04-09T22:09:26.000Z
tags:
  - lumen
categories:
  - PHP
---

# 密码加密与验证

加密

```php
<?php
/**
* 密码入库加密
* @param string $password
* @return string
*
* */
public function passwordEncrypt($password)
{
    return app('hash')->make($password);
}
```

密码验证

```php
<?php
/**
* 密码验证
* @param string $password
* @param string $hashedPassword 加密后的密码
* @return bool
*
* */
public function passwordValidate($password, $hashedPassword)
{
    return app('hash')->check($password, $hashedPassword);
}
```

## 查询数据库判断是否有记录

如果使用 Eloquent 的 [Query Scopes](https://laravel.com/docs/5.2/eloquent#query-scopes) ，查询时使用链式方法调用，通常是这么查询，`$modelObj = Model::id($id)->get()` 来查询指定条件的结果集。但是这么查出来的，实际上返回的是 `Illuminate\Support\Collection` 对象。那么下面的方法，比较适合判断查出来的结果是否存在。

```php
<?php
$modelObj = Model::id($id)->get();

if (! $modelObj->isEmpty()) {
    $modelObj->toArray();
}
//或者这样
if ($modelObj->count()) {
    $modelObj->toArray();
}
```
