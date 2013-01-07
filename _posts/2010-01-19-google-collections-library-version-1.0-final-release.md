---
layout: post
title: "The Google Collections Library was released"
tagline: ""
categories : [develop]
tags: [google, gruva]
---
{% include JB/setup %}


今天看到InfoQ上的一个“旧闻”，是关于google collections的，“The Google Collections Library, version 1.0-final, was released on December 30, 2009”，又再仔细看了一下，看来项目里面应该引入这个包。

- 主要亮点在于：

Immutable Collections，适用于各种并发处理场景
New collections types: Multiset, Multimap, BiMap
Ordering，太方便了，而且代码可读性好多了
Iterables and Iterators，性能应该有改进不少

- 结论(摘自Sune Simonsen)

In my opinion Google Collection will definitely improve your code, making it more readable and save you from creating your own error-prone implementation of the functionality already provided and tested by Google. I think that it is especially good that the library extends the standard library in a non intrusive way, instead of creating a parallel collection library. This also means that you can choose to use only the convenience classes and not spreading Google specific types all over your system if that worries you.

- references:

http://bwinterberg.blogspot.com/2009/09/introduction-to-google-collections.html

http://blog.jayway.com/2009/10/22/google-collections/

--P.S--

@2012.06.02:
迁移自[tanbamboo.blogspot.com博客](http://tanbamboo.blogspot.com/2010/01/google-collections-library-version-10.html)
