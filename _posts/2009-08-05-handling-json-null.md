---
layout: post
title: "Handling JSON NULL"
tagline: ""
categories : [develop]
tags: [json]
---
{% include JB/setup %}

原文URL：[http://extjs.com/forum/showthread.php?t=46336](http://extjs.com/forum/showthread.php?t=46336)

通过为Ext.data.JsonReader的字段加上convert来处理条件映射的问题。

示例如下：

	{"licences":[{"Id":2,"LicenseKey":"123456","Point":null,"Owner":{"Id":1,"Name":"Witbit"}, "Mac":null,"Active":false},

	{"Id":3,"LicenseKey":"789013","Point":null,"Owner":null, "Mac":null,"Active":false}]} 

	this.DS  = new Ext.data.Store(
	{

	    reader: new Ext.data.JsonReader(

	            { 

	                root: "licences",

	                fields: [ "Id", "LicenseKey", {name:"Owner_Name", mapping:"Owner.Name"}, "Point",  "Mac", "Active" ] 

	            })

	}); 

修改Owerne.Name的Mapping方式为下面：

	{name:"Owner_Name", mapping:"Owner", convert: function(v) {return v ? v.Name : null;}}

#### 后记

于2013.01.06 &raquo; 
迁移自[tanbamboo.iteye.com博客](http://tanbamboo.iteye.com/blog/440867)
