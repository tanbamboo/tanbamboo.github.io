---
layout: post
title: "Rails中的重载类变量在production模式和development模式中表现不一致？"
tagline: ""
categories : [develop]
tags: [rails, ruby]
---
{% include JB/setup %}

基类定义类变量
[code="ruby"]
@@default_order = "id asc"
[/code]

子类中定义同一个类变量
[code="ruby"]
@@default_order = "happentime desc"
[/code]

在测试环境里面查询发现是按照happentime降序排列的，切换到production模式发现是按照id升序排列的。

请问是什么原因导致的？

当前通过重载方法的方式，在production和development模式下，都是正常的，暂时用这个方法解决了这个问题。

--P.S--

@2012.06.02:
迁移自[tanbamboo.iteye.com博客](http://tanbamboo.iteye.com/blog/373360)
