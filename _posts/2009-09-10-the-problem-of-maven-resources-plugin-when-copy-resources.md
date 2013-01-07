---
layout: post
title: "Maven插件maven-resources-plugin在copy-resources的时候的Bug"
tagline: ""
categories : [develop]
tags: [maven]
---
{% include JB/setup %}

最近一个应用用到了Jetty SSL方式，开发的时候把测试用的keystore文件放到conf目录下，
然后mvn package进行打包，可是运行的时候发现始终报错：

	java.io.IOException: Invalid keystore format
		at sun.security.provider.JavaKeyStore.engineLoad(JavaKeyStore.java:633)
		at sun.security.provider.JavaKeyStore$JKS.engineLoad(JavaKeyStore.java:38)
		at java.security.KeyStore.load(KeyStore.java:1185)
		at org.mortbay.jetty.security.SslSocketConnector.createFactory(SslSocketConnector.java:215)
		at org.mortbay.jetty.security.SslSocketConnector.newServerSocket(SslSocketConnector.java:423)
		at org.mortbay.jetty.bio.SocketConnector.open(SocketConnector.java:73)
		at org.mortbay.jetty.AbstractConnector.doStart(AbstractConnector.java:283)
		at org.mortbay.jetty.bio.SocketConnector.doStart(SocketConnector.java:147)
		at org.mortbay.component.AbstractLifeCycle.start(AbstractLifeCycle.java:50)
		at org.mortbay.jetty.Server.doStart(Server.java:235)
		at org.mortbay.component.AbstractLifeCycle.start(AbstractLifeCycle.java:50)


通过比较原始的keystore文件和打包后的keystore文件，发现文件大小已经改变了。
看来是maven-resources-plugin在copy-resources的时候的改动的，可能会编码设定有关系。
暂时还没有找到解决方法，Mark一下先。


#### 后记

于2013.01.06 &raquo; 
迁移自[tanbamboo.iteye.com博客](http://tanbamboo.iteye.com/blog/466378)
