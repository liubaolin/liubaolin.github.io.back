---
layout: post
title: IDEA创建maven 一直loading archetype list
category: 技术
tags: IDE
keywords: intellij
description:
---
用IDEA创建Maven工程一直在lodading arhtype list...,加载不出arhtype列表  
解决方案如下:  
Setting---->Build Tools → Maven → Importing, set VM options for importer to -Xmx1024m (默认的是-Xmx512m )
![2016111201](http://ogiythg18.bkt.clouddn.com/2016-11-12ideaMaven.png)
