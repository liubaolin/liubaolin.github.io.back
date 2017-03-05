---
layout: post
title: EJB、JPA和Hibernate
category: 技术
tags: ORM
keywords: Hibernate、ejb、jpa
description:
---

### 1.EJB3.0和JPA
> EJB是sun的JavaEE服务器端组件模型,定义了一个用于开发基于组件的企业多重应用程序的标准.

EJB3.0由不同的部分组成：
* 第一部分为session bean和message-driven bean定义了新的编程模型，以及部署规则等等；
* 第二部分专门定义了持久化相关的规范：实体,对象/关系映射元数据,持久化管理接口和查询语言。第二部分就是我们所说的JPA(Java Persistence API),之所以取名叫JPA，很有可能是因为持久化的接口位于javax.persistence.

>所以，JPA是EJB3.0规范的一部分，是EJB专门为持久化定义的规范。

### 2.EJB3.0和Hibernate的关系

首先你必须要了解的是，一个规范和一个产品是没有太多可比较性的，EJB3.0是java服务器端组件模型的一份规范，而hibernate是一个具体的产品，所以准确的提问应该是：hibernate实现了EJB3.0的规范吗？

正如EJB3.0的规范划分成了不同的部分一样，EJB的实现者也有区分，有些产品完全实现了EJB3.0的规范，而有些产品只是实现了EJB3.0的一部分，比如仅实现了Java Persistence部分。  

Hibernate就是这样的产品，它实现了Java Persistence那部分规范，不仅如此，而且它还提供了一些Java Persistence规范里面没有的一些功能。所以也可以说,JPA规范所对应的功能是hibernate的子集。

> Hibernate实现了JPA规范

实现JPA的不仅仅只有Hibernate EntityManager,还有TopLink,OpenJPA等.

### 3.Hibernate核心模块介绍

1. Hibernate Core   

 Hibernate Core也称为Hibernate3.2.x或者Hibernate.是Hibernate持久化基础服务,带有Hibernate原生的API和它存储在XML文件中的映射数据.并提供HQL查询语言及Criteria和Example的可编程查询接口.是Hibernate其它所有模块的基础.  
 Hibernate Core可以单独使用,应用程序将使用Hibernate原生的Api及xml中的映射元数据.        
2. Hibernate Annotations

 JPA规范定义了orm元数据的语法和语义并在Jdk5.0提供了使用注解的方式定义应用程序元数据.Hibernate Annotation是对jpa定义元数据注解的实现和扩展.    

3. Hibernate EntityManager

 Hibernate EntityManager是基于Hibernate Core之上对JPA的实现.(Hibernate Core提供Hibernate原生Api).

>Hibernate Annotations应该和Hibernate EntityManager结合使用.因为它们都是对JPA的实现,如果用了Hibernate Annotation去创建映射而没有用
Hibernate EntityManager的方式查询编写应用程序是不正常的.


## Hibernate 3.2以及以后的版本开始支持JPA，涉及JPA的子项目有三个，它们分别是：

— Hibernate Core：Hibernate框架的核心实现。

— Hibernate Annotations：支持JDK 5.0的注释。

— Hibernate EntityManager：支持JPA的实现。
