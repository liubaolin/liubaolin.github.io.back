---
layout: post
title:  maven常用插件
category: 技术
tags: Maven系列
keywords: Maven项目
description:
---

# 1.生成javadoc文档包的插件    

    <plugin>  
        <groupId>org.apache.maven.plugins</groupId>  
        <artifactId>maven-javadoc-plugin</artifactId>  
        <version>2.10.2</version>  
        <configuration>  
            <aggregate>true</aggregate>  
        </configuration>  
        <executions>  
            <execution>  
                <id>attach-javadocs</id>  
                <goals>  
                    <goal>jar</goal>  
                </goals>  
            </execution>  
        </executions>  
    </plugin>  


# 2.生成sources源码包的插件    

    <plugin>  
        <artifactId>maven-source-plugin</artifactId>  
        <version>2.4</version>  
        <configuration>  
            <attach>true</attach>  
        </configuration>  
        <executions>  
            <execution>  
                <phase>package</phase>  
                <goals>  
                    <goal>jar-no-fork</goal>  
                </goals>  
            </execution>  
        </executions>  
    </plugin>
