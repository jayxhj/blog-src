---
title: 获取当前日期在本月的第几周
date: 2016-04-26T19:44:42.000Z
tags:
  - php
categories:
  - php
---

# 获取当前日期在本月的第几周

使用 **date()** 函数可以获取某个日期的当前星期数，此星期数为当前日期在一整年中的星期数。假如要获取当前日期在当前月份的星期数，只需用当前日期的星期数减去当前月份第一天的星期数加 1 即可。

## 获取当前日期的星期数

```php
<?php
echo date('W',time());
```

## 获取当前日期是本月的第几周

```php
<?php
/**
 * @param int $date 时间戳
 * @return int 当前日期在本月的第几周
 *
 * */
function weekOfMonth($date) {
    $firstOfMonth = strtotime(date("Y-m-01", $date));
    return intval(date("W", $date)) - intval(date("W", $firstOfMonth)) + 1;
}
```
