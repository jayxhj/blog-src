---
title: Spring-AOP
tags:
  - AOP
  - spring
id: 181
categories:
  - JAVA
date: 2013-03-28 20:45:44
---

加载spring框架和aop框架的支持

配置若干种增强：

1、前置增强（before）；

2、后置增强（After）；

3、返回后增强（after-returning）；

4、抛出异常后增强（after-throwing）；

5、环绕增强（around）；

定位到配置文件中的&lt;aop:config&gt;可以看到如下提示

<input alt="" src="wp-content/uploads/ckfinder/files/2013-03-28_202919.png" type="image" />

Aop的配置如下

&lt;aop:configproxy-target-class=_&quot;true&quot;_&gt;

&nbsp;&nbsp;&nbsp;&nbsp; &lt;aop:aspect&gt;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;aop:beforemethod=_&quot; &quot;_ pointcut=_&quot; &quot;_ /&gt;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;aop:after-returningmethod=_&quot; &quot;_

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; pointcut=_&quot; &quot;_ returning=_&quot;result&quot;_ /&gt;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;aop:after-throwingmethod=_&quot; &quot;_

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; pointcut=_&quot; &quot;_ throwing=_&quot;ex&quot;_ /&gt;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;aop:aroundmethod=_&quot; &quot;_ pointcut=_&quot; &quot;_ /&gt;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;aop:aftermethod=_&quot; &quot;_ pointcut=_&quot; &quot;_ /&gt;

&nbsp;&nbsp;&nbsp;&nbsp; &lt;/aop:aspect&gt;

&nbsp;&nbsp; &lt;/aop:config&gt;

配置文件中method为AspectBean中的方法，pointcut为定义的切入点。

配置后当执行切面Bean时会执行相应的增强。切面Bean的实现类的实例会首先进入配置文件读取切入点的指示符，如果匹配，就执行当前切入点配置的方法。切面编程说白了就是把与业务逻辑无关的一组方法抽象出来并封装，这样实现与业务逻辑的分离。