---
layout: post
title: spring源码学习00
category: 技术
tags: Spring
keywords:
description:
---

阅读源码是一件很艰难的事情,特别是阅读向spring这样复杂的项目,但是阅读好的源码非常有助于能力的提高.希望从这里开始记录spring源码阅读的过程.

## 环境搭建

### 1.从github克隆spring源码,这里克隆的是spring3.2x版本的

    git clone git@github.com:liubaolin/spring-framework.git

### 2.安装gradle

spring现在用gradle构建,所以我们要安装gradle

    sudo dnf install gradle
    gradle -v //安装完后可查看版本

### 3.将源码导入IDE

这里我用的是Intellij IDEA

spring-framework目录下有import-into-idea.md文件,这里面是将spring源码导入idea的教程一步步来即可.
