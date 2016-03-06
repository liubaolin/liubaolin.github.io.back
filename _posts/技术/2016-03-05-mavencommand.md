---
layout: post
title:  Maven常用命令
category: 技术
tags: Maven系列
keywords: Maven项目
description: 
---

　　很多东西都是用熟了之后才去了解他的原理，很多命令都是用多了之后才知道他到底是干什么的，然后过一段时间就忘掉了......

* 创建Maven的普通java项目：

		mvn archetype:create -DgroupId=packageName -DartifactId=projectName

* 创建Maven的Web项目

		mvn archetype:create -DgroupId=packageName -DartifactId=webappName -DarchetypeArtifactId=maven-archetype-webapp  

* 编译源代码

		mvn compile

* 编译测试代码

		mvn test-compile

* 运行测试

		mvn test 


* 仅打包Web页面文件

		mvn war:exploded

* 编译项目

		mvn compile

* 清理（删除target目录下编译内容）

		mvn clean

* 打包发布

		mvn package

* 打包时跳过测试

		mvn package -Dmaven.test.skip=ture

* 发布第三方Jar到本地库中： 

		mvn install:install-file -DgroupId=com -DartifactId=client -Dversion=0.1.0 -Dpackaging=jar -Dfile=d:\client-0.1.0.jar 

