---
title: jsp工程访问路径
tags:
  - jsp路径
id: 83
categories:
  - JAVA
date: 2012-12-19 20:12:22
---

JSP工程在第一次创建时要求输入工程名，默认的访问路径为/project_name，比如工程名为test，则默认的访问路径为http://localhost:8080/test 当然也可以修改路径，需在图中的Contest root URL 内修改:

假如后来无意中修改了工程名，又不记得当初设置的访问路径，那么只需用记事本打开工程的配置文件<span style="color:#ff0000;">.mymetadata</span>就好，里面记载有工程的配置信息。如果需要修改路径，需在文件中修改<span style="color:#ff0000;">context-root</span>项，修改之后重新加载工程才可访问，否则修改不起作用。