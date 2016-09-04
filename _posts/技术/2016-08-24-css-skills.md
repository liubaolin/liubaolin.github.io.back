---
layout: post
title: css使用技巧
category: 技术
tags: 前端开发
keywords:
description:
---
>整理的了一些常用的css技巧,方便快速查阅.前面的部分是直接展示出效果来了,具体实现方式可查阅源代码.

<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <title>css skills</title>
</head>
<body>
  <h3>1.文字居中</h3>
  <div style="width:400px;height:40px;border:1px solid red;text-align:center;line-height:40px">
      文字水平居中
  </div>
  <h3>2.容器的水平居中</h3>
  <div style="width:100px;height:40px;margin:0 auto;border:1px solid red;">
    容器水平居中
  </div>
  <h3>3.文字的垂直居中</h3>
  <div style="height:60px;line-height:60px;width:200px;border:1px solid red;">方式1:文字的垂直居中</div>
  <div style="height:120px;line-height:40px;width:200px;border:1px solid red;">方式2:第一行<br/>第二行</br>第三行</div>
  <div style="height:120px;display:table-cell;vertical-align:middle;width:200px;border:1px solid red;">方式3:文字的垂直居中<br/>第二行文字<br/>第三行</div>
  <h3>4.嵌套容器的居中</h3>
  <div id="big" style="width:200px;height:200px;border:1px solid red;position:relative">
    <div id="small" style="width:50px;height:50px;position:absolute;top:50%;margin-top:-25px;left:50%;margin-left:-25px;border:1px solid green;">

    </div>
  </div>
  <h3>5.图片宽度的自适应</h3>
  <div style="width:80px;height:80px;border:1px solid red;">
      <img src="./img/temp.JPG" style="max-width:100%">
  </div>
  <p>IE6不支持max-width属性,所以在ie6中要使用IE条件注释,并使用width属性</p>
  <h3>6.3D按钮</h3>
  <div style="width:60px;height:30px;border:1px solid;border-color:#999 #666 #666 #999;"/>
    3D按钮
  </div>
  <p>左上边框设置为浅色,右下设置为深色即可出3D效果</p>
  <h3>7.link状态的设置顺序</h3>
  <a href="#" style="">这是一个链接</a>
  <ul>
    <li>a:link</li>
    <li>a:visited</li>
    <li>a:hover</li>
    <li>a:active</li>
  </ul>
  <xmp>
    <style>
      a:link{
        color: blue;
      }
      a:visited{
        color: #FF7F00;
      }
      a:hover{
        color: red;
      }
      a:active{
        color:green;
      }
      xmp{
        background-color: #666;
      }
    </style>
  </xmp>
  <p>需要按照上面的顺序设置</p>
  <h3>8.IE条件注释</h3>
  <div>
    <xmp>
       <!--[if IE]>
    　　　　<link rel="stylesheet" type="text/css" href="ie-stylesheet.css" />
    　　< ![endif]-->
    </xmp>
    <p>还可以区分各种不同的IE版本</p>
    <xmp>
       <!--[if IE 6]> - targets IE6 only -->
    　　<!--[if gt IE 6]> - targets IE7 and above -->
    　　<!--[if lt IE 6]> - targets IE5.5 and below -->
    　　<!--[if gte IE 6]> - targets IE6 and above -->
    　　<!--[if lte IE 6]> - targets IE6 and below -->
    </xmp>
  </div>

  <pre>
    利用条件注释,设置只对IE产生作用的语句
    xmp标签可以让html代码作为字符串在页面里面显示出来；
    pre标签，可以在保留原来文本格式的基础上让文本在页面上显示出来；例如你的文章原来有个换行，如果把它放在pre标签里面，在页面上显示出来的时候还是换行而不需要额外的加换行标签<br/>
  </pre>
  <h3>9.IE兼容性</h3>
  <pre>
    IE6专用语句:方法一
    由于IE6不把html视为文档的根元素，所以利用这一点，可以写出只有IE6才能读到的语句：
  </pre>
  <xmp>
      /* the following rules apply only to IE6 */
  　　* html{
  　　}
  　　* html body{
  　　}
  　　* html .foo{
  　　}
  </xmp>
  <pre>
    IE7专用语句则要写成
  </pre>
  <xmp>
    /* the following rules apply only to IE7 */
　　*+html .foo{
　　}
  </xmp>
  <pre>
    IE专用语句：方法二
    除了IE6以外，所有浏览器都不能识别属性前的下划线。而除了IE7之外，所有浏览器都不能识别属性前的*号，因此可以写出只有这两个浏览器才能读到的语句：
  </pre>
  <xmp>
    .element {
　　　　background: red; /* modern browsers */
　　　　*background: green; /* IE 7 and below */
　　　　_background: blue; /* IE6 exclusively */
　　}
  </xmp>
  <pre>
    IE6的min-height
    IE6不支持min-height，有两种方法可以解决这个问题：
    方法一:
  </pre>
  <xmp>
    .element {
　　　　min-height: 500px;
　　　　height: auto !important;
　　　　height: 500px;
　　 }
  </xmp>
  <pre>
    共有三条CSS语句，第一句是针对其他浏览器设置最小高度，第三句是针对IE设置最小高度，第二句则是让其他浏览器覆盖第三句的设置。
    方法二：
  </pre>
  <xmp>
    .element {
　　　　min-height: 500px
　　　　_height: 500px
　　 }
  </xmp>
  <pre>
    _height只有IE6能读取。
  </pre>
  <h3>10.Text-transform和Font Variant</h3>
  <pre>
    Text-transform用于将所有字母变成小写字母、大写字母或首字母大写.
    Font Variant用于将字体变成小型的大写字母（即与小写字母等高的大写字母）。
  </pre>
  <h3>11.用图片充当列表标志</h3>
  <pre>
    默认情况下,浏览器用一个黑色原点作为列表标志,可以用图片代替它
    list-style	简写属性。用于把所有用于列表的属性设置于一个声明中。
    list-style-image	将图象设置为列表项标志。
    list-style-position	设置列表中列表项标志的位置。
    list-style-type	设置列表项标志的类型。
  </pre>
  <h3>12.将一个容器设为透明</h3>
  <xmp>
    <div style="width:100px;height:100px;background-color:red;">
        <div class="transparency" style="width:50px;height:50px;background-color:#fff">
        </div>
    </div>
  </xmp>
  <pre>
    .transparency{
      filter:alpha(opacity=50);
      opacity: 0.5;　
    }
    第一行是IE专用的
    也可以使用raba实现透明效果,不过IE8不支持
  </pre>
  <h3>超出部分用...表示</h3>
  <xmp>
    <div class="hiddenText" style="width:100px;height:28px;border:1px solid red">
      这是一段超长的文字这是一段超长的文字这是一段超长的文字这是一段超长的文字
    </div>

    .hiddenText{
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;//禁止自动换行
    }
  </xmp>

</body>
</html>
<style type="text/css">
  a:link{
    color: blue;
  }
  a:visited{
    color: #FF7F00;
  }
  a:hover{
    color: red;
  }
  a:active{
    color:green;
  }

　 ul {
　　　list-style-image: url("./img/red-star.png")
　　}
  .transparency{
    filter:alpha(opacity=50);
    opacity: 0.5;　
  }
  .hiddenText{
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;//禁止自动换行
  }
</style>
