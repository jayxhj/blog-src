---
title: Aspectj框架使用Annotation配置AOP
tags:
  - Annotation
  - AOP
  - Aspectj
id: 187
categories:
  - JAVA
date: 2013-03-29 10:34:31
---

1、导入AOP库，里面包含aspectj的库。

2、配置文件中需要配置aspectj的支持（红色标注处）
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans-2.5.xsd 
      http://www.springframework.org/schema/aop 
      http://www.springframework.org/schema/aop/spring-aop-2.5.xsd ">
     <span style="color:#ff0000;">&lt;aop:aspectj-autoproxy />;</span>
    <bean id="aspectBean" class="com.jayxhj.ch08.pojos.AspectBean" />
    <bean id="userService" class="com.jayxhj.ch08.bean.UserServicesImpl" />
</beans>
```

3、在AspectBean中配置切入点
```java
packagecom.jayxhj.ch08.pojos;
 
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
```

<span style="color:#ff0000;">@Aspect //必须放在类的前面</span>

**public** **class** AspectBean {
<p>1、导入 AOP 库，里面包含 aspectj 的库。<br>
2、配置文件中需要配置 aspectj 的支持（红色标注处）<br>
&lt;?xml version=<em>"1.0"</em> encoding=<em>"UTF-8"</em>?&gt;<br>
&lt;beans xmlns=<em>"http://www.springframework.org/schema/beans"</em><br>
    xmlns:xsi=<em>"http://www.w3.org/2001/XMLSchema-instance"</em> xmlns:aop=<em>"http://www.springframework.org/schema/aop"</em><br>
    xsi:schemaLocation=<em>"http://www.springframework.org/schema/beans </em><br>
<em>    http://www.springframework.org/schema/beans/spring-beans-2.5.xsd </em><br>
<em>      http://www.springframework.org/schema/aop </em><br>
<em>      http://www.springframework.org/schema/aop/spring-aop-2.5.xsd "</em>&gt;<br>
    <span style="color:#ff0000;">&lt;aop:aspectj-autoproxy /&gt;</span><br>
    &lt;bean id=<em>"aspectBean"</em> class=<em>"com.jayxhj.ch08.pojos.AspectBean"</em> /&gt;<br>
    &lt;bean id=<em>"userService"</em> class=<em>"com.jayxhj.ch08.bean.UserServicesImpl"</em> /&gt;<br>
&lt;/beans&gt;<br>
 <br>
3、在 AspectBean 中配置切入点 <br>
```java
packagecom.jayxhj.ch08.pojos;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
```
 <br>
<span style="color:#ff0000;">@Aspect //必须放在类的前面</span> <br>
<strong>public</strong><strong>class</strong> AspectBean {<br>
       <span style="color:#ff0000;">@Pointcut("execution(* com..<em>.</em>Service.<em>(..))")</em></span><em>        //切入点表达式 <br>
     <span style="color:#ff0000;">  <strong>private</strong> <strong>void</strong> <u>all()</u> {<br>
       };</span>           //签名 <br>
              //声明签名和切入点表达式 <br>
       <span style="color:#ff0000;">@Before("all()")</span><br>
       <strong>public</strong> <strong>void</strong> checkAuth() {<br>
       }<br>
     <span style="color:#ff0000;">  @After("all()")</span><br>
       <strong>public</strong> <strong>void</strong> release() {<br>
       }<br>
       <span style="color:#ff0000;">@AfterReturning(returning="result",pointcut="execution(</span></em> com..<em>.</em>Service.<em>(..))")<br>
       <strong>public</strong> <strong>void</strong> log(Object result) {<br>
       }<br>
 <br>
       <span style="color:#ff0000;">@AfterThrowing(pointcut="execution(</span></em> com..<em>.</em>Service.*(..))",throwing="ex")<br>
       <strong>public</strong> <strong>void</strong> processException(Throwable ex) {<br>
       }<br>
     <span style="color:#ff0000;">  @Around("all()")</span><br>
       <strong>public</strong> <strong>void</strong> proceedInTrans(ProceedingJoinPoint joinpoint) <strong>throws</strong> Throwable {<br>
       }<br>
}
注意事项：使用aspectj配置增强时AfterReturning和AfterThrowing的参数需匹配。

基于Annotation配置AOP的两种方式:

1. 在每个方法前单独配置；（五种增强都能使用）
2. 将切入点表达式抽离出来，在需要调用时调用；（AfterReturning和AfterThrowing的参数需匹配，不能使用）

 