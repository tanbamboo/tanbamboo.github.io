---
layout: post
title: "Mac平台Java内存泄露问题"
tagline: ""
categories : [develop]
tags: [eclipse, mac, java]
---
{% include JB/setup %}

今天在用Helios Eclipse的时候，突然发现了如下日志信息：

2011-02-14 17:20:10.682 java[549:903] *** __NSAutoreleaseNoPool(): Object 0x10019c520 of class NSConcreteMapTableValueEnumerator autoreleased with no pool in place – just leaking

请教了Google大牛后，搜索到如下记录：

http://lists.apple.com/archives/Java-dev/2010/Nov/msg00229.html

看来已经有人发现了这个问题，Apple也在要求提供重现的backtrace from gdb。

等没事的时候，用GDB来看看Eclipse是否可以重现此问题。

--P.S--

@2012.06.02:
迁移自[tanbamboo.wordpress.com博客](http://tanbamboo.wordpress.com/2011/02/14/mac平台java内存泄露问题/)
