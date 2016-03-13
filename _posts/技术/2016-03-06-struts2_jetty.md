---
layout: post
title: jetty+struts2导致welcome-file-list失效
category: 技术
tags: struts2总结
keywords: Struts2+jetty
description: 
---
　　最近做了一个小项目，本地部署后可正常访问（tomcat+Struts2），输入http://ip:port/appName后可正常跳转到web.xml的welcom-file-list中配置的默认访问页面index.jsp，但是部署到新浪云平台后，输入<font color="green">http://ip:port/appName</font>访问报错，如下

	：There is no Action mapped for namespace / and action name . - [unknown location]

　　改输入<font color="green">http://ip:port/appName</font><font color="red">/index.jsp</font>后可正常访问，开始怀疑容器未自动访问index.jsp，web.xml中配置的默认访问页面失效。

　　因为新浪云的web容器用的是jetty，于是在本地换jetty服务器测试，发现报同样的错误，反复检查struts2配置未发现明显的配置错误。

　　	浪费了好多时间才发现<font color="red">jetty+struts2会导致welcome-file-list失效</font>，可以通过struts的<font color="red">default-action-ref</font>配置解决此问题。

	
	<!-- 未找到action时，默认跳转到此action -->
	<default-action-ref name="index" /> 
	
	<!-- 去登录页面 -->
	<action name="index">
		<result name="success">
			/index.jsp
		</result>
	</action>

　　注意：

1. 如果把default-action-ref配置在一个有命名空间的package下，那么它只对这个命名空间下的没有action的访问起作用，所以我们应该把它默认的命名下才能对所有的没有找到的action起作用。

2. 我们最好不要通过配置它来代替错误页面，因为它返回的状态码是200，如果你的项目放在互联网中，搜索引擎会会认为你是在作弊，从SEO的角度是不利的。我们可以在default-action-ref配置之前配置错误页面等相关页面，如：

		<!-- 定义全局的Result -->
		<global-results>
			<!-- 重定向到登录页面 -->
			<result name="loginInter" type="redirectAction">
				<param name="namespace">/login</param>
				<param name="actionName">toLoginAction</param>
			</result>
			<!-- 定义全局的错误页面 -->
			<result name="error">
				/WEB-INF/error.jsp
			</result>
		</global-results>

