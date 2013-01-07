---
layout: post
title: "Custom Spring Security Filter"
tagline: ""
categories : [develop]
tags: [spring security, spring, java]
---
{% include JB/setup %}

最近在项目中用Spring Security 2.0.1做权限控制管理，由于以前用过Acegi Security，基本上很快搞定。但是后来在移植自己扩展的AuthenticationProcessingFilter的时候，发现了很多问题。
经过一些网络搜索和测试，问题解决，做个记录：

1. 如果有自定义的Filter用于替换系统默认filter的时候，需要把http的auto-config选项关闭掉，否则会导致与默认filter冲突，Order相同。
2. http配置里面的anonymous、login、logout等，实际上是配置了过滤器链中的默认的AnonymousFilter、authenticationProcessingFilter、logoutFilter等。本例中由于仅扩展authenticationProcessingFilter，所以只需要去掉login配置。
3. login配置还包括了一个authenticationProcessingFilterEntryPoint的概念，所以当替换掉默认的authenticationProcessingFilter之后，需要在http中，通过属性entry-point-ref指向一个authenticationProcessingFilterEntryPoint。

由此可见，其实spring security在架构上还是和acegi一脉相承的，只是spring security在配置方面做了更多的magic处理，优点是：入门容易，配置内容更少；缺点是：增加了magic成分，也增加了查找错误、扩展的难度。

#### 后记

于2013.01.06 &raquo; 
迁移自[tanbamboo.iteye.com博客](http://tanbamboo.iteye.com/blog/199470)

---