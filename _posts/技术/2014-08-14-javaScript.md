---
layout: post
title: 03：javaScript基础
category: 技术
tags: 前端开发
keywords: javaScript
description: 

---
　　前两篇介绍了html、div和css，通过他们可以做出精美的前台页面，但是再精美也只能是静态页面，缺少交互，本文介绍javaScript（简称js），是用html做前端的必备技术，本文可作为javaScript快速入门知识来看（biao吐槽排版，这是之前的做的一篇笔记~~）。

　　ps：顺便吐槽一句，记得当时我学js时总是搞不明白js和jsp的区别，javaScript和java的区别，直到后来长大了，我才渐渐的明白，原来，，原来，，它们之间半毛钱的关系也米有，坑爹呀，javaScript只是为了提高知名度才强行用了java么。


###一、JavaScript简述

      1、javaScript--js，是一种
                               基于对象和事件驱动的、
                               解释性的、
                               脚本语言，嵌入到页面上
      2、特征：html+CSS+js--需要浏览器查看
                     标准的规范代码
      3、如何书写

<font color="red" >

       a、事件定义：代码直接写在事件中
			demo:
			onclick=function(){fun(){
					//这里直接写方法
			}}
       b、script块：代码写在页面的head里的script里，使用方法封装，事件中调用方法
                           ----适用于当前页面的重用
       c、js文件：代码写在单独的js文件里，使用方法封装
                       页面先引入，后调用方法
</font>


###二、基础语法

      1、标识符的规范-----方法的名称、变量的名称
               a、 字母、数字、下滑线、 $  ，不能以数字开头
               b、 不能和系统保留关键字重复
      2、代码的书写规范：----大小写敏感，以；结束
      3、变量的声明-----js是一种松散类型的语言,声明变量使用var，变量的数据类型以赋值为准
  
           var a1="mary";
           var a2=100;
           var a3=true;
      4、数据类型
                简单类型：string、number、boolean
           
                特殊类型：null、undefind

                复杂类型：Array等
      5、数据类型的转换

                a、各种简单类型
                          string:以一对单引号或双引号

                          特殊字符：使用转义

                          中文：\u4e00表示一个汉字，用于那些不允许使用中文的表达式中，比如正则表达式

                          number:  var a=1;  var 1=2.3;

                          boolean:  true/false    1/0(计算)
                b、隐式转换：将不同类型的数据，自动转换
                               string+number----string
                               string+bool---string(true/false)
                               number+bool-----number
                               bool+bool-----number(1/0)

                c、显示转换：调用方法实现转换
                               parseInt     如果不能转换，则返回NaN(Not a Number)
                                                经常要结合isNaN（）方法使用，用于判断文本是否可以转换为数值
                               parseFloat
                               toString
      6、运算符
                a、各种运算符
                b、比较运算符中，特殊

                          a==b      比的是值                  相等-------值
                          a===b   比的是值和类型         严格相等----值和类型

                          if(0 == "")      ---true
                c、三目运算符
                       ？   ：；

      7、流程控制：改变默认的执行顺序
                a、分支
                          if/else
                          swtich/case
                b、循环
                          for
                          while

      alert("Hello");    Hello---字面值
      var a="Hello";
      alert(a);


三、常用内置对象
     
     1、js中的对象：内置对象
     2、String对象
               a、创建

                         var s = "mary"

                         var s = new String("mary")
               b、属性
               
                         s.lengh
               
               c、方法
               
                         s.charAt()/charCodeAt()
                         s.replace("old","new")
                         s.split(",")
                         s.toUpperCase()/toLowerCase()
                         s.indexOf/lastIndexOf("a")
                         s.substring(start,end)   

               d、字符串，需要结合正则表达式实现特殊的功能

                                        replace（替换）/search(找到匹配的字符串的位置）/match（得到匹配的所有结果的数组）

                         正则表达式：\d{3}       [a-z]{3,5}          ------纯文本类型的表达式
                                        表示一种匹配，不同的应用

                                        js中使用正则表达式： /正则表达式/标识符               

                         添加标识符：g     gloal               全部
                                             i     ignource          忽略大小写
                                             m   multiline           多行

                            

     3、Array对象
               a、创建和初始化

                         var a = new Array();   可以写长度也可以不写
                              a[0] = "mary";
                              a[1] = 100;
                              a[2] = true;

                         var a = new Array("mary",100,false);

                         var a = ["mary",100,false];

                         二维数组：
                         
                         var a = new Array();
                         a[0] = ["1","2"]

               b、属性

                    a.length

               c、方法

                    a.toString()---------mary,100,false       
                    a.join("|") ---------连接   mary|100|false

                    a.sort() ---------排序
                                          默认的比较规则：按照字符比
                    a.reverse() --------反转 

                    a.concat();  -------合并数组

                    a.slice();  ---从已有的数组中返回选定的元素



----------
**<font color="red">--------------------------------插播一段demo-------------------------------</font>**

	此处不允许出现js，录入完毕，替换为*
	  <input type="text" onblur="filterString();" id="txtStr"/>             //失去焦点事件
	//过滤敏感字符
	     function filterString()
	{
	     var str = document.getElementById("txtStr").value;
	     var r = str.replace("js","*");         ---------只能替换一次
	     document.getElementById("txtStr").value = r;           //把等号右边的值赋给等号的左边（设置文本框中的值）
	}
	
	
	function filterString()
	{
	     var str = document.getElementById("txtStr").value;
	     var r = str.replace(/js/gi,"*");                                           //正则表达式和标识符的应用
	     document.getElementById("txtStr").value = r;
	}
	
	     //过滤所有的数字
	     var r = str.replace(/\d/g,"*");
	     //不允许录入中文
	     var r = str.replace(/[\u4e00-\u9fa5]/g,"*");                   注意中文的表示方式
	
	
	//实现对数字的各种操作
	function operArray(t){
	     var str = document.getElementById("txtArr").value;
	     var arr = str.split(",");
	     switch (t)
	     {
	     case 1:
	          arr.reverse();
	          break;
	     case 2:
	          arr.sort();
	          break;
	     case 3:
	          arr.sort(sortFunc);
	          break;
	     }
	     alert(arr.join("*"));
	}
	//方法：定义比较规则
	function sortFunc(a,b)
	{
	     return a-b;
	}

----------

     4、math对象
               a、不需要创建，直接使用

               b、属性和方法

                    Math.random();--------------产生的随机数范围  [0,1)

                    Math.round()/floor()/ceil()

     5、数值对象
               a、创建
                    var n = 123;
                    var n = 123.456789
               b、方法
                    n.toString();

                    n.toFixed(n);     -------------数值的格式化输出，保留小数点后的n位数

     6、正则表达式对象

                         

               a、声明   /表达式/gim

               b、应用
                              应用一：集合String对象的方法，常用于对于字符串的处理
                         
                              应用二：使用正则表达式的test方法，用于实现录入的验证
                                             
                                             var r = /\d{3}/;

                                            r.test(string)--------true/false

**<font color="red">--------------------------------demo-------------------------------</font>**

	  用户名：文本框   3-5个大小写字母    
	     var name = document.getElementById("valiName").value;
	     var r = /^[a-zA-Z]{3,5}$/;               注意^和$  表示从头至尾
	     if(!r.test(name)){
	          alert("录入错误")
	     }


     7、日期对象

          a、创建
                         var d = new Date();------当前日期和时间
                    
                         var d = new Date("2013-9-10");

          b、方法
                         getxxx();获取
                                   getFullYear();
                                   getMonth();
                                   getDate()

                         setxxx();修改

                         toxxx();获取一定格式的字符串，常用于显示
                                       toString();

                                       toLocalTimeString();      //只获取时间
                                       tolocalDateString();
     8、方法
          
               a、封装代码

               b、如何定义方法  function methodName（x,y....）{方法体  //return value;}

               c、调用方法
                                   M();                             无参无返回
                                   M(1,3);                        有参无返回
                                   var r = M(20);              有参有返回
                    
               d、局部变量和全局变量

               e、重载：在js中没有传统的重载，若方法名相同，则后定义的方法将覆盖先定义的方法。

                              提供了关键字arguments，表示传入方法中的所有参数，可以模拟实现方法的重载

                                        arguments是一种特殊的对象，在函数代码中，表示函数的参数数组

               f、方法的其它定义方式

                      其它方式一：var f = new Function("参数","方法体")；

                                         f()

                       其它方式二： 匿名函数/方法-------方法体更好做维护
                                             var f = function(x,y){
                                                      alert(x+y);
                                             };
**<font color="red">--------------------------------demo-------------------------------</font>**

	function m()
	{
	     var n = arguments.length;
	     if(n==1)
	     {
	          var a = arguments[0];
	          alert(a*a);
	     }
	     else if(n == 2)
	     {
	          alert(arguments[0]+arguments[1]);
	     }
	}
	
	function testMethod(){
	     var arr1 = [12,3,4,0,123,33];
	
	     var f = new Function("x","y","return x-y");
	     arr1.sort(f);
	     alert(arr1.toString());
	    
	     var arr2 = [3,43,33,2,13,9];
	
	     var f = function(x,y){                   //匿名方法/函数
	          return x-y;
	     }
	     arr2.sort(f);
	     alert(arr2.toString());
	}

     9、全局函数

          a、所有的对象均可使用

          b、常用

               parseInt/parseFloat

               isNaN

          c、     decodeURI/encodeURI


          d、eval ：用于计算某个字符串，以得到结果
                         或者用于执行其中的JavaScript代码

               等

###四、DHTML应用
	
　　这里有必要说一下，当初在学习的时候搞的很混乱，主要是纠结与概念，什么是DHTML啊，什么是DOM对象啊，什么是window对象啊，其实没有必要纠结于概念，刚开始你可以把所有这些都对象都归结为DHTML技术体系的一部分，而DHTML就是一种是HTML页面具有动态特性的技术。

	  1、什么是DHTML应用：动态效果 对大多数人来说，DHTML 意味着 HTML、样式表和 JavaScript 的组合。
	
	  2、DHTML对象
	
	                      window
	
	                      event
	
	                      history
	
	                      screen
	
	                      location
	
	                      navigation         以上都是BOM:浏览器对象模型
	
	                      document--------html文档  DOM:文档对象模型
	
	
	  3、window对象
	
	            a、表示浏览器窗口，窗口实现交互的功能
	
	            b、弹出对话框功能
	                      window.alert();
	
	                      window.confirm();  确认，防止用户误操作
	            
	            c、弹出窗口
	
	                      window.open(url,name,"width=500");
	
	                      window.close();
	
	            d、定时器方法
	                      周期性定时器
	
	                           var t = window.setInterval(exp,1000);
	
	                           window.clearInterval(t);
	                      
	                      一次性定时器
	
	                           var t = window.setTimeout
	
	                           window.clearTimeout()
	                      
	  4、document对象------DOM文档对象模型--标准
	            
	                 a、将整个文档作为一颗节点树 结构
	
	                 b、查询
	
	                           方式一：document.getElementById("");-----单个
	
	                           方式二：根据节点的层次关系
	
	                                obj.parentNode
	                                obj.childNodes/firstChild/lastChild  
	
	                           方式三：根据标签的名称查找----节点的数组
	
	                                document.getElementsByTagName("a");
	                                obj.getElementsByTagName("a"); ------obj对象范围内
	
	                                适用于查找某种元素，而无关于层次位置
	
	                 c、obj.nodeName------返回节点的名称，全大写的方式
	
	                                          适用于节点的类型未知的情况
	   
	
	                 d、读取或者修改节点的信息-------适用于节点类型已知的情况
	                           
	                           方式一：将html标签对象化
	
	                                文本框.value           <input type="text" value=""/>
	
	                                图像.src                  <img src=""/>
	
	                                连接.href                 <a href="">xxx</a>
	
	                           方式二：得到标签中间的文本
	
	                                obj.innerHTML
	
	                           方式三：单个样式
	
	                                obj.style.color="red";               <p type="color:red">text</p>
	
	                                obj.style.backgroundColor="silver";
	
	                                obj.style.fontSize = 30;
	
	                           方式四：样式比较复杂
	                                
	                                  JS:  h1.className="s1"
	
	                                 CSS:  h1.s1{XXXX}       
	
	                                 THML:   <h1>text</h1>
	
	                 e、增加新节点
	
	                           创建：  document.createElement("a");    -------<a></a>
	
	                           设置：   obj.href="";   obj.innerHTML="";
	
	                           加入：   XXXX.appendChild(obj);          ---作为XXXX对象的最后一个子节点加入
	
	                                       XXXX.insertBefore(obj,oldChild);       将obj对象放在XXXX子对象oldChild的前面
	
	
	                         小知识点：select对象  onchange事件       getElementById("select1").selectedIndex;   当前选中的下拉项的索引
	
	                 f、删除子节点
	
	                                     xxxx.removeChild(obj);  ---obj必须是xxxx的子节点
	
	                                     obj.parentNode.removeChild(obj);
	
	   5、HTML DOM --了解
	          a、  标准的js代码，在实现上较为复杂------实现第三方的封装，Jquery、HTML DOM
	
	            b、HTML DOM :将一些常用的DOM操作进行了封装
	
	           c、标准DOM 方式：为表格添加一行，行中两个单元格
	                                          document.createElement("tr");
	                                             document.createElement("td");
	                                                document.createElement("td");
	
	            d、换为  HTML DOM 中的table对象
	                                table.insertRow(index);         返回新的行对象
	
	                                table.rows------------table.getElementByTagName("tr");
	
	                                row.insertCell(index);     返回单元格对象
	
	  6、screen对象  -------封装的的是跟屏幕的分辨率相关的
	                 var r = screen.height;  //ok  ----read
	                 screen.height = 500;   //error ---不能设置
	
	                 screen.availWide
	
	  7、history对象----历史访问记录
	
	                 var n = history.length;---------历史记录（url)数
	
	                 history.back();//单击“后退”按钮
	
	                 history.forward();//前进
	
	                 history.go(-2);//单击两次后退
	
	  8、location对象-------浏览器的地址栏
	  
	                 var url = location.href;---------获取当前页面的url地址
	
	                 location.href = "a.html"; --------修改当前访问的url地址，保留历史访问记录
	
	         location.replace(url)-----会去往新的url地址，没有历史访问记录
	
	         location.reload()://重新载入当前的网址，同按下刷新按钮
	
	       --------------------页面间跳转的方法
	       
	                        静态方式：
	                                <a></a>
	                        代码方式：
	                                window.open()-----------一定是在新窗口中
	
	                                location.href         -------是否需要保留访问记录
	                                location.replace()
	
	                                history.xxx()---------------必须有历史记录才能使用
	       9、navigator---浏览器软件以及操作系统的相关信息
	                            只提供了可读的属性
	
	                 a、js中访问对象的属性有两种方式
	                           location.href
	                           location["href"]
	
	  b、遍历某种对象的所有属性
	       for(var pName in navigator)
	       {
	           
	       }
	            
	
	
	       10、event
	
	                 a、事件：响应的机制，通过某种动作实现对代码的调用
	
	                 b、事件的分类
	                           鼠标事件
	                                          onclick/onddlclick/onmouseover/onmouseout           单/双   鼠标移入/移出
	
	                           键盘事件
	                                          onkeyup/onkeydown
	
	                           状态事件
	
	                                          onblur/onfocus/onchange/onload(body)/onunload
	
	                 c、定义事件
	
	                           <元素 onxxx=""/>   -----html  静态的
	
	                           obj.onxxx=function;------js代码的方式为元素定义事件
	
	
	                 d、事件可以被取消 
	
	                           onxxx="return false";----------取消事件
	
	                 e、事件有冒泡机制
	
	                           当为拥有层次关系的元素定义了相同的事件时。最底层的被触发，层层向上
	
	                           ----当大量的子节点拥有相同的事件时，考虑将事件定义在父元素中
	
	                 f、event对象：当事件发生时，当前事件相关的信息
	
	                                event.x/y-----得到事件发生的位置点
	
	                                event.srcElement----事件源元素
	
	                                第一个问题：得到event对象
	
	                                     在firtfox：js中不认识event对象，在html页面上认识（需要在方法中传入event对象）       event.target
	
	                                          firfox也不认识srcElement，它的表示方式是event.target
	
	                                     其它浏览器在html和js中都认识event
	
	                                第二个问题：获得对象的相关信息
	
	                                     事件的源元素对象
	                                          其它：event.srcElement
	                                          火狐：event.target
	                                          解决方案：二者 ||
	
	                    
	                                
	                      所以在js中event.srcElement || event.target         ---注意这里的event在js中是参数传进来的 
	
	                 g、浏览器的兼容问题
	
	                                写标准代码
	
	                                特殊问题特殊处理


###五：面向对象基础

      1、封装：相关的数据和行为打包

      2、方式一：使用Object
                               --实现比较简单，重用性较差，适用于数据的简单封装

**<font color="red">--------------------------------demo-------------------------------</font>**

	function testObj()
	{
	     var obj = new Object();
	     obj.name = "mary";
	     obj.age = 23;
	     obj.eat = function(){
	                         alert("eat");       
	                    }
	     //测试
	     alert(obj.name + ":"+obj.age);
	     obj.eat();
	}
          
	3、方式二：自定义对象
                                 --重用性好，类似于类的定义，适用于封装需要重用时

**<font color="red">--------------------------------demo-------------------------------</font>**
              
	//定义Person"类"
	function Person(name,age){
	     this.name = name;
	     this.age = age;
	     this.introduceSelf = function(){
	                                  alert("i am "+name+" my age is "+age);
	                              }
	}
	//测试自定义类
	function testOwnObj(){
	     var p1 = new Person("mary",12);
	     var p2 = new Person("john",23);
	     alert(p1.name);
	     p1.introduceSelf();
	     alert(p2.age);
	     p2.introduceSelf();
	}
        --------可以实现封装，页面及js范围内（客户端）

　　
      
	4、如果需要将数据发送到服务器端：xml、JSON(JavaScript Object Notation)--是一种轻量级的数据交换格式

      方式三：

![14081４01](/public/img/tec/2014-08-14_js01.jpg)

	 	名称可以是属性，字符串类型的属性需要用“”引起来
	
	    名称也可以是方法--为其赋值function对象

**<font color="red">--------------------------------demo-------------------------------</font>**


	//测试JSON封装数据
	function testJSON()
	{
	     var obj = {
	          "name":"mary",
	          "age":18,
	          "isGra":true,
	          "eat":function(){
	                         alert("eat");
	                    }
	     };
	     //测试json
	     alert(obj.name+":"+obj.age+":"+obj.isGra);
	     obj.eat();
	}

	function testDOM()
	{
	     //修改段落
	     var pObj = document.getElementById("p1");  //先获取段落对象
	     pObj.style.color = "red";
	     pObj.style.backgroundColor = "gray";
	     pObj.style.innerHTML = "aaadfads";
	     //修改图像
	     var imgObj = document.getElementById("m1");
	     imgObj.src = "admin_out.png";
	     //修改复杂样式
	     var h1Obj = document.getElementById("h1");
	     h1Obj.className = "s1";
	}


###小知识点：

	onxxx = "return false"--------取消事件
	
	<a href="javascript:cancleFunc()"></a> ---------给超级链接加事件


