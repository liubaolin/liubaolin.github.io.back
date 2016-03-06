---
layout: post
title: 03：maven打包
category: 技术
tags: Maven系列
keywords: Maven配置
description: 
---



　　在Eclipse左侧右击项目，Debug As -> Debug Configurations -> 双击Maven Build 然后看弹出框的右侧

　　右侧的Name随便填写一个名字，
Basedirectory 则选择项目路径
Goals是Maven文档里会有说明的目标动作，一般填"clean install"就是可以了。填的时候不要引号
然后点击Debug

　　Maven就会开始打包了，打的包会放在与src同级的target目录下。

　　如果这个项目对别的项目有依赖，则要先打包别的项目成,再打包当前项目。
然后把这个目录下的内容放到服务器就好了。放好后要重启服务器上的tomcat才可以。

　　![16030506](/public/img/tec/2016-03-05_mavenpackage.jpg)

