---
layout: post
title: IDEA创建maven多模块项目
category: 技术
tags: 开发工具
keywords: intellij
description:
---
### 1.创建一个空的project--multyMavenModulesApp

我们在这个project中包含项目开发过程中所有的模块

![2016111202](http://ogiythg18.bkt.clouddn.com/2016-11-12-ideaMultiMaven01.png)

![2016111203](http://ogiythg18.bkt.clouddn.com/2016-11-12-ideaMultiMaven02.png)
### 2.创建一个maven父工程--webapp-parent,统一管理所有的jar
![2016111204](http://ogiythg18.bkt.clouddn.com/2016-11-12-ideaMultiMaven03.png)

父工程webapp-parent只是管理jar，选择  
quickstart 骨架

![2016111205](http://ogiythg18.bkt.clouddn.com/2016-11-12-ideaMultiMaven04.png)

![2016111206](http://ogiythg18.bkt.clouddn.com/2016-11-12-ideaMultiMaven05.png)

完成后我们手动晚上项目结构,并ctrl+shift+alt+s
配置项目结构,如下:  

![2016111207](http://ogiythg18.bkt.clouddn.com/2016-11-12-ideaMultiMaven06.png)

### 3.创建子模块webapp

该模块是一个webapp工程,我们用maven-archetype-webapp原型创建  

![2016111208](http://ogiythg18.bkt.clouddn.com/2016-11-12-ideaMultiMaven07.png)

注意下面的"add module to"选的是none,而不是webapp-parent,这样webapp就不会在webapp-parent下面了,这样结构更清晰,webapp在结构上是一个独立的子工程

![2016111209](http://ogiythg18.bkt.clouddn.com/2016-11-12-ideaMultiMaven08.png)

然后一直下一步最后创建出webapp子工程  
至此idea创建maven的多模块项目就结束了,这只是一个最简单的情况.通常一个真正的多模块maven项目会比这个复杂一些,比如会有一个父模块专门管理jar包,父模块下面的子模块也是一个专门管理jar包的二级父模块,在这个父模块下面就是我们一个个的业务模块.总之具体的项目结构在实际的开发中去设计,这里只是罗列idea中建立maven多模块的操作,抛砖引玉.大体思路如此.

### 4.工程建好了,如何在idea中发布配置web项目

参见[Intellij IDEA搭建javaweb项目](http://baolinliu.com/2016/08/27/idea01.html)
