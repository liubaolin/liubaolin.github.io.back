---
layout: post
title: Spring-WS-01简介
category: 技术
tags: Spring-Webservice
keywords: spring webservice
description: 
---
说明：本文根据《Spring 3.x 企业应用开发实战》学习整理

###Spring-WS简介

　　Spring WebService（简称Spring-WS）是Springframework东家SpringSource公司旗下的一个子项目，是专注于“基于文档驱动”（document-driven）的Web Services，并帮助契约优先（contract-first）的SOAP服务开发，允许利用操作XML Payloads的多种方法之一来创建灵活的Web Services，目前最新的稳定版本是2.2.1，可以从springsource.org下载。

###Spring-WS特性

　　Spring-WS是应用文档驱动（契约优先）的思想来构建灵活的Web Services的推动者之一，它从传统的基于代码优先的Web Service开发模式中吸取各种教训，提供了契约和实现之间的松耦合，没有强制将服务契约直接连接到某一个业务类，而是用你擅长的任何方式（DOM、SAX、StAX、甚至XML映射组件如JAXB、Castor、JIBX、或者XMLBeans）来处理Web服务报文请求，并通过服务端点调用服务业务逻辑。此外，获取请求映射到服务端点的方式，完全由开发人员自己决定，默认情况下Spring提供基于消息内容的映射。总之，它具有以下一些特性：

* Spring框架的全面支持，你可以用Spring的所有特性；
* Spring-WS使用契约优先（contract-first）的设计模式，不支持通过代码导出服务；
* 支持几乎所有的XML API,处理传入XML消息的时候不限于JAXP（Java API for XML Processing），可以选择你所擅长的XML API;
* 灵活的XML编组（Marshalling），支持各种OXM技术，如JAXB、XMLBean、Castor等；
* Security支持，集成了Acegi Security，实现Web Services认证；
* 提供一个简单易用的Web服务模板操作类**WebServiceTemplate**；

###Web Services的开发模式

目前Web Services的开发有两种模式：一种是代码优先的开发模式(Contract Last);另一种是契约优先的开发模式（Contract First)。	

* 代码优先的开发模式

　　代码优先的开发模式是传统Web Services开发世界中最常见、最容易上手的一种开发模式，也是大多数开发人员喜欢的一种开发模式。其开发过程相对比较简单，之所以简单，是应为它迎合了开发人员的惰性。代码驱动Web 服务构建是通过相关类库（如CXF等）直接导出服务类的Web Service调用接口，绕过编写数据契约（XSD)及SOAP的繁琐过程。

　　Spring提供了很多服务导出器，可将现有的Bean导出基于RMI或HTTP远程调用服务，但还没有提供一个直接将Bean导出为Web Services的导出器，然而我们可以结合一个优秀的第三方SOAP开源框架（如CXF)，就可以将Bean导出响应的Web服务。（关于CXF的使用，请移步本站WebService标签下，里面有CXF的使用介绍）。

* 契约优先的开发模式
