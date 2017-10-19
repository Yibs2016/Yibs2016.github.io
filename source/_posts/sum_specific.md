---
title: sum_specific
date: 2017-02-09
tags:
---
## JS
* 原型模式：每个函数都有原型(prototype),prototype通过调用构造函数而创建的那个对象实例的原型对象 <br/>


  >  作用： 让所有对象实例共享它所包含的属性和方法  <br/>
  >  区别[与构造函数]：
     - 构造函数中，每个实例都会拷贝构造函数，浪费内存
     - 原型中，所有的实例共享prototype中的定义，所有实例的属性指向的是同一个对象的引用

###### example:
``` bash
    function sayHi() {
        alert("hi");
     }
    sayHi.sayHello = function() {
        alert("hello");
     };
     sayHi.sayHello();//outputs hello
     var osayHi = new sayHi();//outputs hi
     osayHi.sayHello(); // TypeError: osayHi.sayHello is not a function
```

<!--more-->
不是通过在prototype中以这样的方式添加的(sayHi.prototype.sayHello),实例是不能访问sayHello方法的，只能通过sayHi.sayHello(类似如静态方法)的方式访问。同样如果添加的时候是通过prototype方式，则不能用静态方式访问。
```
   function sayHi() {
       alert("hi");
   }
   sayHi.prototype.sayHello = function() {
       alert("hello");
   };
   var osayHi = new sayHi();// outputs hi
   osayHi.sayHello(); // outputs hello
   sayHi.sayHello(); //TypeError: sayHi.sayHello is not a function