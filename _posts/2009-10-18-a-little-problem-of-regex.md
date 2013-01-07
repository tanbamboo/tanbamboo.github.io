---
layout: post
title: "一个Regex错误导致的问题，郁闷。"
tagline: ""
categories : [develop]
tags: [regex, java]
---
{% include JB/setup %}

最近有个项目，被老板追的很紧，结果我在代码重构(用Regex优雅的替换了一些方法)后，由于测试不充分，结果现场出现一个问题。

正解： 

	([^\$\{\}]*)\$(\{([\w\d]*)\}|([\d]+))
误写为： 

	([^\$\{\}]*)\$(\{([\w\d]*)\}|([\d+]))
	
结果group 4总是只能取第一个数字，立刻想到了Java最长匹配、最短匹配的问题，google了n久没有结果，实在受不了，压力之下，有了以下的代码片段：

	int begin = 0;
	int pos1 = -1;
	int pos2 = -1;
	int pos3 = -1;
	int pos4 = -1;
	int pos5 = -1;
	for(int i = 0; i < format.length(); i++) {
		char c = format.charAt(i);
		if(c == '$') {
			pos1 = i;
		}
		if(pos1 != -1 && c == '{') {
			pos2 = i;
		}
		if(pos2 != -1 && c == '}') {
			pos3 = i;
			prefixAndProperties.add(new PrefixAndProperty(pos1 > begin?format.substring(begin, pos1):"", format.substring(pos2 + 1, pos3), null));
			begin = i + 1;
			pos1 = pos2 = pos3 = -1;
		}
		if(pos1 != -1 && pos4 == -1 && c >= '0' && c <= '9') {
			pos4 = i;
		}
		if(pos4 != -1 && (c < '0' || c > '9')) {
			pos5 = i;
			prefixAndProperties.add(new PrefixAndProperty(pos1 > begin?format.substring(begin, pos1):"", null, format.substring(pos4, pos5))); 
			begin = i;
			pos1 = pos4 = pos5 = -1;
		}
	}
	if(pos1 != -1 && pos4 != -1) {
		prefixAndProperties.add(new PrefixAndProperty(pos1 > begin?format.substring(begin, pos1):"", null, format.substring(pos4))); 
	} else {
		if(begin < format.length()) {
			suffix = format.substring(begin);
		}
	}

最后和另一个同事讨论了一下，居然发现是+写到了`[]`里面， :cry: 

#### 后记

于2013.01.06 &raquo; 
迁移自[tanbamboo.iteye.com博客](http://tanbamboo.iteye.com/blog/493520)
