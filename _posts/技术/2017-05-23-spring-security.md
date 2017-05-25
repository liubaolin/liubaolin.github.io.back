---
layout: post
title: spring-security札记
category: 技术
tags: Spring
keywords:
description:
---

[参考文档](http://www.tianshouzhi.com/api/tutorials/spring_security_4/250)


## 前言

应用级别的安全主要分为 **验证( authentication)** 和 **授权 (authorization)** 两个部分

Authentication指的是建立规则（Principle）的过程，Principle可以是用户、设备或者其它要对我们的应用进行某种操作的系统。

Authorization指的是某个Principle在我们系统中是否被允许执行某个操作。在进行授权之前要求相应的Principle在验证的过程中已经被建立好了。

这些概念都是通用的，并不只针对spring-security

## 依赖

## 配置


## 词汇
* 验证 authentication
* 授权 authorization
