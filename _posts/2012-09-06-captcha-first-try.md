---
layout: post
title: "Captcha初接触"
tagline: ""
categories: [develop]
tags: [captcha, cage, recaptcha]
---
{% include JB/setup %}

上个周五晚上，接到一个电话，说是有个临时的项目，需要帮忙开发一下。

周期4天，初步估计需要四个开发人员。

## 项目简述

一个验证码服务系统，也就是Captcha as a Service，算是SaaS的一种应用。

主要是两个部分：
- 验证码生成与验证接口
- 后台管理

因为我团队大家都没有验证码或者图片相关开发经验，所以我还是决定自己来做“验证码生成与验证接口”部分，然后把后台管理分解给其他几个同事。

简单的google了一下captcha相关资料，有了如下信息：
- [cage](http://akiraly.github.com/cage/)
- [simple captcha](http://simplecaptcha.sourceforge.net)
- [kaptcha](http://code.google.com/p/kaptcha/)
- [jcaptcha](http://jcaptcha.sourceforge.net)
- [recaptcha](http://recaptcha.net)

除了以上的5个相关第三方库之外，还看到了基于AWT/Graphic2D的验证码实现的两个文档：
- J道人生的博客：[java验证码生成器,自认为还算经典](http://www.blogjava.net/wuxingjia/archive/2008/04/05/190893.html)
- ITEye上的博客：[动态验证码生成](http://ypf3027.iteye.com/blog/370847)

recaptcha已经是一个Captcha as a Service了，所以直接放弃了。不过的确是做的很好的系统，主要是立意高远：

>It's Useful. Why waste the effort of your users? reCAPTCHA helps to digitize books.

而且接口做的很好，可以参考[reCAPTCHA API Documentation](https://developers.google.com/recaptcha/intro)。

大概半天后，搞定了两种验证码，基于cage的，和基于AWT/Graphic2D的。

其他的由于更多是面向WEB应用的，而且文档不是很齐备，所以就先放放。

cage效果不错，变形、旋转、模糊、阴影效果都支持，发现4种效果齐开的时候，那真不是人用的。^_^

awt方式的效果一般，由于不熟悉image相关包，简单的实现了字体颜色、字体等等。

对比后，毫不犹豫的选择了cage。代码跟进去看了一下，似乎也还比较简单，主要是用到了[GlyphVector](http://docs.oracle.com/javase/1.4.2/docs/api/java/awt/font/GlyphVector.html)，来处理以上说的各种效果。

回到故事上来，再一天就是调校颜色，这真是一个复杂活。

按照传统的RGBA的方式，试验了n次，又n次，再n次，还是惨不忍睹，就在快放弃的时候，看到同事的PS上有个HSB的色彩空间，试了一下，那个效果真是出奇的好。

HSB就是为相似色处理而生的。

>HSL stands for hue, saturation, and lightness, and is often also called HLS. HSV stands for hue, saturation, and value, and is also often called HSB (B for brightness).

想深入了解的请移步[Wikipedia上的HSL and HSV词条](http://en.wikipedia.org/wiki/HSL_and_HSV)

顺便了解了浏览器CSS是否支持HSL色彩空间，似乎是可以的，太激动了，可以好好的研究一下。

至此任务就基本完成了。

下面贴几张效果图

![效果图](http://i45.tinypic.com/30krty0.jpg)