---
layout: post
title: "Discussion about SIEM"
description: ""
category: [siem] 
tags: [siem, arcsight, splunk, elk]
---
{% include JB/setup %}

最近看到关于SIEM的一个讨论，原文链接为:
http://skizzlesec.com/2014/06/08/security-analysts-discuss-siems-elasticsearchlogstashkibana-vs-arcsight-splunk-and-more/

其中有几点非常有意思

# 1. Free-from, Human driven VS. Workflow-Driven, Automated

If you’re into free-form, human driven, what-if searches and explorations then Splunk (or any other free-form log manipulation toolset) will fare much better. Setting up alerting utilizing regexes and defined known-bad strings in a SIEM is not the same, your success comes from the intelligence the human users running it have.
If you want workflow-driven, automated, audit-able and repeatable first-line alerting for well defined conditions (and running defined regexe’s against data realtime is defined conditions IMO) then you’ll fare better with a SIEM. You won’t do as good if you want to drill into any of the great patterns provided on this list on the fly with most SIEMs though; Splunk et al will most likely outperform.


-Randy

# 2. 

# 3.

# 4.

# 未完待续

后续如果有时间，考虑把这个讨论翻译为中文，国内SIEM的应用层次还太低，这种讨论更是难得一见。
国内SIEM产品厂商虽然多，但是相关业内人士的交流沟通太少。
