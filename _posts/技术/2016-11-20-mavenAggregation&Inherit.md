layout: post
title: 05：maven聚合和继承
category: 技术
tags: Maven系列
keywords: Maven配置
description:
---

### 一、为什么要聚合？

随着技术的飞速发展和各类用户对软件的要求越来越高，软件本身也变得越来越复杂，然后软件设计人员开始采用各种方式进行开发，于是就有了我们的分层架构、分模块开发，来提高代码的清晰和重用。针对于这一特性，maven也给予了相应的配置。    
>情景分析一：    

我们在开发过程中，创建了2个以上的模块，每个模块都是一个独立的maven project，在开始的时候我们可以独立的编译和测试运行每个模块，但是随着项目的不断变大和复杂化，我们期望能够使用简单的操作来完成编译等工作，这时Maven给出了聚合的配置方式。    

所谓聚合，顾名思义，就是把多个模块或项目聚合到一起，我们可以建立一个专门负责聚合工作的Maven project ---  aggregator。


#### 建立该project的时候，我们要注意以下几点：         
1. 该aggregator本身也做为一个Maven项目，它必须有自己的POM
2. 它的打包方式必须为： pom
3. 引入了新的元素：modules---module
4. 版本：聚合模块的版本和被聚合模块版本一致
5. relative path：每个module的值都是一个当前POM的相对目录
6. 目录名称：为了方便的快速定位内容，模块所处的目录应当与其artifactId一致(Maven约定而不是硬性要求)，总之，模块所处的目录必须和<module>模块所处的目录</module>相一致。
7. 习惯约定：为了方便构建，通常将聚合模块放在项目目录层的最顶层，其它聚合模块作为子目录存在。这样当我们打开项目的时候，第一个看到的就是聚合模块的POM
8. 聚合模块减少的内容：聚合模块的内容仅仅是一个pom.xml文件，它不包含src/main/Java、src/test/java等目录，因为它只是用来帮助其它模块构建的工具，本身并没有实质的内容。
9. 聚合模块和子模块的目录：他们可以是父子类，也可以是平行结构，当然如果使用平行结构，那么聚合模块的POM也需要做出相应的更改。

### 二、为什么要继承？   

做面向对象编程的人都会觉得这是一个没意义的问题，是的，继承就是避免重复，maven的继承也是这样，它还有一个好处就是让项目更加安全

>情景分析二：    

我们在项目开发的过程中，可能多个模块独立开发，但是多个模块可能依赖相同的元素，比如说每个模块都需要Junit，使用spring的时候，其核心jar也必须都被引入，在编译的时候，maven-compiler-plugin插件也要被引入

#### 如何配置继承：    

1. 说到继承肯定是一个父子结构，那么我们在aggregator中来创建一个parent project
2. <packaging>: 作为父模块的POM，其打包类型也必须为POM
3. 结构：父模块只是为了帮助我们消除重复，所以它也不需要src/main/java、src/test/java等目录
4 .新的元素：<parent> ， 它是被用在子模块中的
5. <parent>元素的属性：<relativePath>: 表示父模块POM的相对路径，在构建的时候，Maven会先根据relativePath检查父POM，如果找不到，再从本地仓库查找
6. relativePath的默认值： ../pom.xml
7. 子模块省略groupId和version： 使用了继承的子模块中可以不声明groupId和version, 子模块将隐式的继承父模块的这两个元素

### 三、可被继承的POM元素    

* groupId:项目组ID,项目坐标的核心元素    
* version: 项目版本, 项目坐标的核心元素
* description: 项目的描述信息
* organization: 项目的组织信息
* inceptionYear: 项目的创始年份
* url: 项目的URL地址
* developers: 项目开发者信息
* contributors: 项目的贡献者信息
* distributionManagement: 项目的部署配置
* issueManagement: 项目的缺陷跟踪系统信息
* ciManagement: 项目的持续集成系统信息
* scm: 项目的版本控制系统信息
* mailingLists: 项目的邮件列表信息
* properties: 自定义的maven属性
* dependencies: 项目的依赖配置
* dependencyManagement: 项目的依赖管理配置
* repositories: 项目的仓库配置
* build: 包括项目的源码目录配置、输出目录配置、插件配置、插件管理配置等
* reporting: 包括项目的报告输出目录配置、报告插件配置等

### 四、maven的依赖管理    

我们知道dependencies是可以被继承的，这个时候我们就想到让我们的发生了共用的依赖元素转移到parent中，这样我们又进一步的优化了配置。可是问题也随之而来，如果有一天我创建了一个新的模块，但是这个模块不需要这些parent的依赖，这时候如何处理？

是的，maven的依赖管理就是来解决这个问题的
增加一个新的元素：**dependencyManagement**

从上面的列表中我们发现dependencyManagement也是可以被继承的，这恰恰满足了我们的需要，它既能够让子模块继承到父模块的依赖配置，又能保证子模块依赖使用的灵活性
dependencyManagement的特性：在dependencyManagement中配置的元素既不会给parent引入依赖，也不会给它的子模块引入依赖，仅仅是它的配置是可继承的

> 最佳实践：    

这时候我们就可以在父POM中声明这些依赖：

    <properties>  
        <target.version>2.5.6</target.version>
    </properties>  

    <dependencyManagement>  
        <dependencies>  
            <dependency>  
                <groupId>your groupId</groupId>  
                <artifactId>your artifactId</artifactId>  
                <version>${target.version}</version>  
            </dependency>  
        </dependencies>  
    </dependencyManagement>  

子模块的POM继承这些配置：子模块继承这些配置的时候，仍然要声明groupId和artifactId,表示当前配置是继承于父POM的，从而直接使用父POM的版本对应的资源

     <dependencies>  
          <dependency>  
              <groupId>your groupId</groupId>  
              <artifactId>your artifactId</artifactId>  
          </dependency>  
      </dependencies>  

这个可以有效的避免多个子模块使用依赖版本不一致的情况，有助于降低依赖冲突的几率。注：只有子模块配置了继承的元素，才会真正的有效，否则maven是不会加载父模块中声明的元素。
### 五、Maven的插件管理    

再增加一个新的元素：**pluginManagement**
这个元素和<dependencyManagement>相类似，它是用来进行插件管理的。
在我们项目开发的过程中，也会频繁的引入插件，所以解决这些复杂配置的方法就是使用插件管理
我们可以在父POM中做如下声明：

    <build>  
      <pluginManagement>  
          <plugins>  
              <plugin>  
                  <groupId></groupId>  
                  <artifactId></artifactId>  
                  <version></version>  
                  <executions>  
                      <execution>  
                          <id></id>  
                          <goals>  
                              <goal></goal>  
                          </goals>  
                          <phase></phase>  
                          <configuration>  
                              <source></source>  
                              <target></target>  
                          </configuration>  
                      </execution>  
                  </executions>  
              </plugin>  
          </plugins>  
      </pluginManagement>  
    </build>  


在子模块中，我们可以直接继承

    <build>  
          <plugins>  
              <plugin>  
                  <groupId>org.apache.maven.plugins</groupId>  
                  <artifactId>maven-compiler-plugin</artifactId>  
              </plugin>  
          </plugins>  
     </build>  

我们会发现，继承的细节上和<dependencyManagement>几乎一样。

### 六、聚合与继承的总结  

当我们明白聚合与继承的细节之后，我们会发现：
对于聚合模块来说，它知道有哪些被聚合的模块，而对于被聚合的模块来说，它们不知道被谁聚合了，也不知道它的存在
对于继承关系的父POM来说，它不知道自己被哪些子模块继承了，对于子POM来说，它必须知道自己的父POM是谁
在一些最佳实践中我们会发现：一个POM既是聚合POM，又是父POM，这么做主要是为了方便。
