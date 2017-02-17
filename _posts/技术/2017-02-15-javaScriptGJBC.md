---
layout: post
title: 《javaScript高级程序设计(第三版)》(javaScript循序渐进02)
category: 技术
tags: 前端开发
keywords:
description:
---

>《javaScript高级程序设计》 --javaScript循序渐进第二本


## chapter two(在html中使用javaScript)

* &lt;script&gt;元素    

  > 所有&lt;script&gt;元素都会按照它们出现的顺序依次被解析.在不适用defer和async属性的情况下只有在解析完&lt;script&gt;元素中的代码后才会解析后面的&lt;script&gt;元素中的代码

* &lt;script&gt;标签的defer属性    

  > 立即下载,但是等到整个页面都解析完成后再执行.   
    defer只适用于外部脚本.(HTML5中明确规定)    
    延迟脚本总是按照它们的顺序执行    
    把延迟脚本放在页面底部,仍然是最佳选择

* &lt;script&gt;的async属性    

  > async表示不必阻塞其它脚本,也不阻塞文档呈现.不  能保证异步脚本按照它们在文档中的顺序执行

* 引用外部js文件(而非直接在html中嵌入js代码)的优点:    

  > 可维护性    
    可缓存    
    适应未来:HTML和XHTML引入外部js文件的语法是相同的

* &lt;noscript&gt;    

  > 用以在不支持或禁用javaScript的浏览器中展示替代的内容.    
  在支持并启用了javaScript脚本的浏览器中不会显示&lt;noscript&rt;元素中的内容

## chapter three(基本概念)

* javaScript中的一切都区分大小写   

* 严格模式    

  > ES5中引入了严格模式    
    在严格模式下,ES3中的一些不确定的行为将得到处理,并且对某些不确定的操作也会跑出错误    
    要在整个脚本中启用严格模式,在顶部添加代码"use strict"即可(老版本的浏览器会把它当作一行普通字符串，加以忽略)    

* 数据类型    

  * 5中简单的数据类型(也称之为基本类型)    

    > Undefined、Null、Number、String、Boolean         任何数值除以0都将返回NaN    
     NaN与任何值都不相等,包括NaN本身    
     可以用isNaN()函数判断,isNaN()会尝试将参数转换为数值,不能转换成数值的值都会导致这个函数返回true

  * 1种复杂数据类型:Object    

    > Object本质上是由一组无名的键值对组成的    
    Object的每个实例都具有以下的属性和方法:      

      * Constructor:构造函数    

      * hasOwnProperty(propertyName):用于检查给定的属性是否在当前的实例中    

      * isPropertyTypeOf(object):传入的对象是否是另一个对象的原型    

      * propertyIsEnumerable(propertyName):给定的属性是否能够用for-in语句类枚举    

      * toLocalString:返回该对象的字符串表示,该字符串与执行环境的地区对应.    

      * toString返回对象的字符串表示.    

      * valueOf():返回对象的字符串、数值、或布尔值表示.通常与toString返回的值相同.     

    > 由于BOM和DOM都是宿主对象,因为它们是由宿主实现提供和定义的.ECMA-262不负责定义宿主对象,因此宿主对象可能会也可能不会继承Object    


* switch语句    

  > javaScript中可以在Switch语句中使用任何数据类型       
  每个case的值可以是常量、变量、甚至是表达式    
  switch语句在比较值是时候用的是全等操作符    

* javaScript函数中所有参数传递的都是值,不可能通过引用传递    

* javaScript函数(由于不存在方法签名)不能像传统意义那样实现重载

## chapter four(变量、作用域和内存问题)

* javaScript函数参数是值传递、值传递、值传递!!

      function setName(obj){
      	obj.name='richey';
        obj = new Object();
        obj.name = 'kelly';
      }

      var person = new Object();
      setName(person);
      alert(person.name);//richey

* type操作符是确定一个变量是字符串、数值、布尔值、还是undefined的最佳工具.如果变量的值是一个对象或者null,则typeof操作符会返回"object"    

* javaScript没有块级作用域!!!    

      if(true){
      	var color = "blue";
      }
      alert(color); //blue    

      function add(num1,num2){
      	var sum = num1 + num2;
        return sum;
      }
      var result = add(10,20);
      alert(sum); //由于sum不是有效的变量,这里会报错d

  > 使用var声明的变量会自动被添加到最近的环境中.在函数内部,最近的环境就是函数环境.如果初始化时没有用var声明,该变量会被添加到全局环境.(不建议这么做,而且严格模式下不允许这么做)

* 优化内存占用的最佳方式,就是为执行中的代码只保存必要的数据.一旦数据不用,最好将其设置为null,以释放其引用.这种做法叫解除引用.局部变量会在它们离开环境时自动解除引用.

* 解除引用,并不意味着自动回收该值所占用的空间.解除引用的真正目的是让其值脱离执行环境,以便垃圾收集器在下次执行的时候将其回收.    

## chapter five(引用类型)

* 引用类型的值(对象)是引用类型的一个实例    

* 引用类型是一种数据结构,将数据和功能组织在一起.(它常被称为类)

* Object类型    

* Array类型    

  > 数组中的每一项可以保存任意类型的数据    
    数据的大小可以动态调整,即随着数据的添加自动增长    
    数组的length属性不是只读的.通过设置这个属性,可以已出数组末尾的数据     
    ECMAScript5新增了Array.isArray()方法,用来确定某个值是不是数组.   
    栈方法(LIFO):push()/pop()    
    队列方法(FIFO):push()/shift()/unshift()    
    排序方法:reverse()  sort()(默认字符串自然排序)       

          var arr = [1,2,3];
          arr.length = 2;
          arr[arr.length] = 'a';
          arr[arr.length] = 'b';
          alert(arr);    

          var arr = [1,2,3,4,5];
          var sum = arr.reduce(function(prev,cur,index,array){
          	return prev + cur;
          })
          alert(sum); //15     

* Date类型    

  > ECMAScript5添加了Date.now()方法,返回调用这个方法时日期的毫秒数    


      //本地时间2005年5月5日下午5:55:55
      var date = new Date(2005,4,5,17,55,55);

      var start  = Date.now();
      var _start = +new Date(); //用+操作符把Date对象转换成字符串,可以达到同意的目的

* RegExp类型    

* 创建正则表达式    

  > var expression = /pattern/flags;    
    其中模式(pattern)部分可以是任何简单或复杂的正则表达式.每个正则表达式都可以带一个或多个标志(flags),匹配模式支持下列三种标志:    

    * g:表示全局模式,即模式会被应用到所有字符串,而非发现第一个匹配项就停止;    
    * i:忽略大小写模式    
    * m:多行模式,即在到达一行文本的末尾时,会继续查找下一行中是否有匹配模式的项.      


        var pattern1 = /\[bc\]at/i; //字面量的方式
        var pattern2 = new RegExp("\\[bc\\]at","i");//使用Regexp构造函数       

  > ECMAScript5规定使用字面量和使用构造函数RegExp一样,每次都创建新的RegExp实例.        
* RegExp实例属性    

  > lastIndex:整数,表示开始搜索下一个匹配项的字符位置,从0开始    

* RegExp实例方法    

  > exec():接受一个参数(即要应用模式的字符串),返回包含第一个匹配项信息的数组(或者在没有匹配项的情况下返回null)     
  返回的虽然是Array的实例,但包含两个额外的属性index(表示匹配项在字符串中的位置)和input(表示应用正则表达式的字符串).     
  对于exec()方法而言,即使模式表达式中设置了全局标志(g),它每次也只会返回一个匹配项.    
  在不设置全局标志的情况下,同一个字符串多次调用exec(),将始终返回第一个匹配项信息.    
  而设置了全局标志的情况下,每次调用exec()方法都会在字符串中继续查找新的匹配项    


        var text = "mom and dad and baby";
        var pattern = /mom (and dad (and baby)?)?/gi;

        var matches = pattern.exec(text);
        alert(matches.index);//匹配项在字符串中的位置 0
        alert(matches.input);//应用正则表达式的字符串 mom and dad and baby
        alert(matches[0]);//mom and dad and baby
        alert(matches[1]);//and dad and baby
        alert(matches[2]);//and baby


        var text = "cat,bat,sat,rat";
        var pattern1 = /.at/;

        var matches = pattern1.exec(text);
        alert(matches.index);       //0
        alert(matches[0]);          //cat
        alert(pattern1.lastIndex);  //0

        var matches = pattern1.exec(text);
        alert(matches.index);       //0
        alert(matches[0]);          //cat
        alert(pattern1.lastIndex);  //0

        var pattern2 = /.at/g;
        var matches = pattern2.exec(text);
        alert(matches.index);       //0
        alert(matches[0]);          //cat
        alert(pattern1.lastIndex);  //0

        var matches = pattern2.exec(text);
        alert(matches.index);       //4
        alert(matches[0]);          //bat
        alert(pattern2.lastIndex);  //7       

  > IE的javaScript在实现lastIndex上存在偏差,即使在非全局模式下lastIndex属性每次也会变化       
  > test()方法    

* Function    

  > 函数声明与函数表达式:解析器会率先读取函数声明,并使其在执行任何代码之前是可用的(可以访问);至于函数表达式,则必须要等到解析器执行到它所在的代码行时,才会被解释执行.    

      函数声明方式:    
      function sum(num1,num2){
        return num1 + num2;
      }
      函数表达式定义函数的方式:
      var sum = function(num1,num2){
        return num1 + num2;
      }

      alert(sum(10,10));//20
      function sum(num1,num2){
        return num1 + num2;
      };
      alert(sum(10,10));//报错
      var sum = function(num1,num2){
        return num1 + num2;
      };


  >  


## SUMMARY    

* Array.isArray()方法,用来确定某个值是不是数组.    
