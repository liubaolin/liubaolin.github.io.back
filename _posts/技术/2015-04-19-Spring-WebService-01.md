---
layout: post
title: Spring-WS-02体系及重要API
category: 技术
tags: Spring-Webservice
keywords: Spring-WS API
description: 
---

###WebServiceMessage

	org.springframework.ws
	Interface WebServiceMessage

　　WebServiceMessage是Spring-WS中一个核心接口，代表一个与具体传输协议无关的XML消息，该接口提供了获取有效负载（palyload）消息的方法。

	◆ 获取消息中的Result内容
		Result	getPayloadResult() 

	◆ 获取消息中的Source内容
		Source	getPayloadSource() 


　　其中javax.xml.transform.Source及javax.xml.transform.Result表示XML的一组输入输出接口，对于不同的XML解析模型都有相应的实现类。	如下图所示：
	

![150419](/public/img/tec/xml-transform.jpg)
   
	◆ 将整个消息写到输出流
		 void	writeTo(OutputStream outputStream) 

###SoapMessage

	org.springframework.ws.soap 
	Interface SoapMessage

　　SoapMessage是Spring-WS中的另一个核心接口，代表一个机遇SOAP协议的XML消息。它扩展字MimeMessage接口，MimeMessage接口又扩展自WebServiceMessage接口。MimeMessage提供了多用途网际邮件扩展协议（MIME）操作接口方法，如添加消息附件 MimeMessage.addAttachment()及访问消息附件MimeMessage.getAttachment()。SoapMessage提供了SOAP操作接口方法



###Web Services的开发模式

目前Web Services的开发有两种模式：一种是代码优先的开发模式(Contract Last);另一种是契约优先的开发模式（Contract First)。	

