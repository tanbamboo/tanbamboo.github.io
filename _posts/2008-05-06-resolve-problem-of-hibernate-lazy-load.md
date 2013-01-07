---
layout: post
title: "Hibernate的延迟加载的解决"
tagline: ""
categories : [develop]
tags: [java, hibernate, orm]
---
{% include JB/setup %}

最近项目里面的Hibernate采用了延迟加载的方式，但是老是出现Session已经关闭的问题。通过google发现可以采用OpenSessionInViewFilter方式解决。但是加入了osiv过滤器后，问题依旧存在，经过几天的测试，配置了各种hibernate.transaction_class和hibernate.current_session_context_class方式后，问题依旧。

通过分析OpenSessionInViewFilter的源码，发现问题的真实所在：osiv中使用的session是通过`SessionFactoryUtils.getSession(sessionFactory, true)`获取的，并且在请求开始的时候设置FlushMode为never，在请求结束时进行flush处理；而我在DAO中是采用`sessionFactory.getCurrentSession()`方式获取的。初步判断问题是这两种方式获取的session是不通的，导致dao获取的PO对象，在延迟加载的时候，session由于transaction的commit而关闭了。

解决方案:在DAO中与osiv方式相同的采用`SessionFactoryUtils.getSession(sessionFactory, true)`获取session，结果延迟加载问题解决。

#### 后记

于2013.01.06 &raquo; 
迁移自[tanbamboo.blogspot.com博客](http://tanbamboo.blogspot.com/2008/05/hibernate.html)
