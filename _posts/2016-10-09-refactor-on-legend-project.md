---
layout: post
title: "重构一个遗留项目"
tagline: ""
categories : [java]
tags: [codestyle, refactor, architecture]
---
{% include JB/setup %}

[TOC]

# 现状

## 代码拷贝

项目采用了微服务(Microservice)架构，按照业务进行服务垂直划分。

很多个服务模块，是通过复制整个项目修改而来，所以必然存在两个坏味道(Bad Smell):

- 重复的代码，比如Helper类都没有抽离出来作为lib，
- 无用的代码，拷贝过来后不再用到的代码

## 模糊不清的API接口



# 重构思路

## 梳理、改进Service API

## 抽取公用逻辑

## 修改服务路由

# 重构