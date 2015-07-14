
---
layout: post
title: webservice_01_schema约束
category: 技术
tags: WebService
keywords: schema规范
description: 
---

###Schema规范
1. 所有的标签和属性都要有schema文件来约束
2. 所有的schema文件都要有一个namespace（可以理解为它的id）
3. namespace的值由targetNamespace属性来指定，一般是一个url格式的值
4. 如何引入一个schema约束？

		属性：xmlns
		属性值：schema文件的namespace值

5. 如果引入的schema文件不是W3C组织定义的，则要指定schema文件的位置
6. schema文件的位置由什么属性来指定？
				
		属性：schemaLocation（该属性由W3C定义，所以还要引入W3C定义的schema的namespace）
		属性值：xmlns path（要引入的schema的targetNamespace 路径）

7. 如果引入了n个命名空间，则至少要给n-1个取别名

###DEMO
* schema文件
	
		<?xml version="1.0" encoding="UTF-8"?>	
		<schema xmlns="http://www.w3.org/2001/XMLSchema" <!-- 指定约束文件 -->
				targetNamespace="http://www.richey88.com/bookXMLSchema" <!--命名空间-->
				elementFormDefault="qualified">
	
		<element name="书架">
			<complexType>
				<sequence maxOccurs="unbounded">
					<element name="书">
						<complexType>
							<sequence>
								<element name="书名" type="string" />
								<element name="作者" type="string" />
								<element name="售价" type="string" />
							</sequence>
						</complexType>
					</element>
				</sequence>
			</complexType>
		</element>
	</schema>

* xml文件
		
		<?xml version="1.0" encoding="UTF-8"?>
		<书架 xmlns="http://www.richey88.com/bookXMLSchema" 
			 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
			 xsi:schemaLocation="http://www.richey88.com/bookXMLSchema book.xsd">
			<书>
				<书名>Spring in Action</书名>
				<作者>Craig Walls</作者>
				<售价>￥59</售价>
			</书>
		</书架> 


