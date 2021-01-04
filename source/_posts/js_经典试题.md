---
title: 经典试题
---

#### 原型继承 函数声明提升 运算符优先级
```js
function Foo() {
  getName = function() {
    //全局变量
    console.log(1);
  };
  return this;
}
Foo.getName = function() {
  //构造函数 属性
  console.log(2);
};
Foo.prototype.getName = function() {
  console.log(3 && 33);
};
var getName = function() {
  //变量覆盖
  console.log(4);
};
function getName() {
  //函数声明优先 重复定义被忽略，重写函数体被覆盖
  console.log(5);
}
Foo.getName(); //2
getName(); //4
Foo().getName(); //1   再次覆盖
getName(); //1
new Foo.getName(); //2  Foo.getName()后，再new一个实例
new Foo().getName(); //33  new Foo() 实例__proto__指向Foo的prototype
new new Foo().getName(); //33  new (new foo().getName())
```

<!--more-->
