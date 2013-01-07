---
layout: post
title: "Handling JSON NULL"
tagline: ""
categories : [develop]
tags: [json]
---
{% include JB/setup %}

原文：[url]http://extjs.com/forum/showthread.php?t=46336[/url]

通过为Ext.data.JsonReader的字段加上convert来处理条件映射的问题。

示例如下：
[code="java"]
{"licences":[{"Id":2,"LicenseKey":"123456","Point":null,"Owner":{"Id":1,"Name":"Witbit"}, "Mac":null,"Active":false},

{"Id":3,"LicenseKey":"789013","Point":null,"Owner":null, "Mac":null,"Active":false}]} 
[/code]

[code="java"]
this.DS  = new Ext.data.Store(
{

    reader: new Ext.data.JsonReader(

            { 

                root: "licences",

                fields: [ "Id", "LicenseKey", {name:"Owner_Name", mapping:"Owner.Name"}, "Point",  "Mac", "Active" ] 

            })

}); 
[/code]

修改Owerne.Name的Mapping方式为下面：
[code="java"]
{name:"Owner_Name", mapping:"Owner", convert: function(v) {return v ? v.Name : null;}}
[/code]

--P.S--

@2012.06.02:
迁移自[tanbamboo.iteye.com博客](http://tanbamboo.iteye.com/blog/440867)
