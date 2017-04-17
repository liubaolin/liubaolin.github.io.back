---
layout: post
title: ES6新特性——箭头函数
category: 技术
tags: 前端开发
keywords:
description:
---

## 箭头函数（Lambda表达式）    

* ES6中引入了一种编写函数的新语法

      // ES5
      var selected = allJobs.filter(function (job) {
       return job.isSelected();
      });
      // ES6
      var selected = allJobs.filter(job => job.isSelected());    

* 如果要写一个接受多重参数（也可能没有参数，或者是不定参数、默认参数、参数解构）的函数，你需要用小括号包裹参数list。    

      // ES5
      var total = values.reduce(function (a, b) {
        return a + b;
      }, 0);
      // ES6
      var total = values.reduce((a, b) => a + b, 0);    

* 除表达式外，箭头函数还可以包含一个块语句    

      // ES5
      $("#confetti-btn").click(function (event) {
        playTrumpet();
        fireConfettiCannon();
      });

      // ES6
      $("#confetti-btn").click(event => {
        playTrumpet();
        fireConfettiCannon();
      });    

  注意，使用了块语句的箭头函数不会自动返回值，你需要使用return语句将所需值返回。    
  小提示：当使用箭头函数创建普通对象时，你总是需要将对象包裹在小括号里。    

      // 为与你玩耍的每一个小狗创建一个新的空对象
      var chewToys = puppies.map(puppy => {});   // 这样写会报Bug！
      var chewToys = puppies.map(puppy => ({}));     

  不幸的是，一个空对象{}和一个空的块{}看起来完全一样。ES6中的规则是，紧随箭头的{被解析为块的开始，而不是对象的开始。因此，puppy => {}这段代码就被解析为没有任何行为并返回undefined的箭头函数。    
  JavaScript引擎会将类似{key: value}的对象字面量解析为一个包含标记语句的块。幸运的是，{是唯一一个有歧义的字符，所以用小括号包裹对象字面量是唯一一个你需要牢记的小窍门。    

## 箭头函数的this值    

* 普通function函数和箭头函数的行为有一个微妙的区别，箭头函数没有它自己的this值，箭头函数内的this值继承自外围作用域。    

* 无论是否需要，function函数总会自动接收一个this值。你是否写过这样的hack代码：    

      {
        ...
        addAll: function addAll(pieces) {
          var self = this;
          \_\.each(pieces, function (piece) {
            self.add(piece);
          });
        },
        ...
      }    

  在ES6的版本中，注意addAll方法从它的调用者处获取了this值，内部函数是一个箭头函数，所以它继承了外围作用域的this值。    
  超赞的是，在ES6中你可以用更简洁的方式编写对象字面量中的方法，所以上面这段代码可以简化成：

      // ES6的方法语法
      {
        ...
        addAll(pieces) {
          \_.each(pieces, piece => this.add(piece));
        },
        ...
      }
