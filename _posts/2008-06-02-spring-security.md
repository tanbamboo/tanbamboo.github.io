---
layout: post
title: "Spring Security的https、http通道过滤器"
tagline: ""
categories : [develop]
tags: [spring security, spring, java]
---
{% include JB/setup %}

在使用Spring Security的时候，还发现了一个关于http、https通道的问题。

目的：通过https提交登陆数据，登陆成功后再转换回http。
问题：开发过程中，使用Jetty作为测试的Web Container，http、https切换都没有问题，满足需求。但是当部署到Tomcat 5.5后，发现登陆成功后，依旧跳转到登陆页面。

经过跟踪发现，在登陆成功后，从https跳转回http的时候，Session丢失，导致问题的出现。搜索后的结果是，tomcat在处理http、https的session的时候，为了防止安全漏洞，禁止把https通道的session用于http。

暂时没有找到好的解决办法，可能需要采用其他方式解决。

#### 后记

于2013.01.06 &raquo; 
迁移自[tanbamboo.iteye.com博客](http://tanbamboo.iteye.com/blog/199479)
