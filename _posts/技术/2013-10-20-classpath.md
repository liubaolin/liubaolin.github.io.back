---
layout: post
title: 关于classpath的解释
category: 技术
tags: 一些重要的细节
keywords: classpath
description: 
---

　　以前在开发过程中经常会被classpath搞的晕头转向，这里特作出总结，这些都属于基础性的细节。
##1.classpath是什么
　　classpath 类的路径，在编译运行java程序时，如果有调用到其他类的时候，在classpath中寻找需要的类。

　　所以，src不是classpath, WEB-INF/classes,lib才是classpath
WEB-INF/ 是资源目录, 客户端不能直接访问。

　　这话是没错，不过现在的**IDE编译器在编译时会把src下的文件（是文件，不是.java）移到WEB-INF/classes下**。但这是可以设置的，项目右键 -〉properties-〉java build path->source ,这个下面有一个default output folder,**这个地方的设置为类的输出路径即classpath**，通常设置如下：

	项目名称/WebContent/WEB-INF/classes 

　　值得注意的是，spring配置文件里这个locations是uri表示，也就是说你写的jdbc.properties是当前相对路径，要访问classpath记得要这样写：<value>classpath:jdbc.properties</value>

##2.解释classes含义
1. 存放各种资源配置文件 eg.init.properties log4j.properties struts.xml
2. 存放模板文件 eg.actionerror.ftl
3. 存放class文件对应的是项目开发时的src目录编译文件
总结：这是一个定位资源的入口

##3.classpath和classpath*的区别 
* classpath：只会到你指定的class路径中查找找文件;
* classpath*：不仅包含class路径，还包括jar文件中(class路径)进行查找.

　　举个简单的例子，在我的web.xml中是这么定义的：classpath*:META-INF/spring/application-context.xml

　　那么在META-INF/spring这个文件夹底下的所有application-context.xml都会被加载到上下文中，这些包括META-INF/spring文件夹底下的 application-context.xml，META-INF/spring的子文件夹的application-context.xml以及jar中的application-context.xml。

　　如果我在web.xml中定义的是：classpath:META-INF/spring/application-context.xml

　　那么只有META-INF/spring底下的application-context.xml会被加载到上下文中。
　　




