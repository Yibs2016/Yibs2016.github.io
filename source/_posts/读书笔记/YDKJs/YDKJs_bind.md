---
title: bind 与 softbind
categories: 
- [读书笔记, YDKJs]
---

#### Function.prototype.bind(..)

> 1.创建新的包装函数——忽略当前 this,并把提供的对象绑定到 this. 2.可把除第一个参数（第一个用于绑定 this）外的其他都传给下层的函数（这种技术称为“部分应用”，是“柯里化”的一种）。

##### 实现

```js
if (!Function.prototype.bind) {
  Function.prototype.bind = function(oThis) {
    if (typeof this !== "function") {
      // 与 ECMAScript 5 最接近的 内部 IsCallable 函数
      throw new TypeError(
        "Function.prototype.bind - what is trying " +
          "to be bound is not callable"
      );
    }
    var aArgs = Array.prototype.slice.call(arguments, 1), //bind上的参数截取
      fToBind = this,
      fNOP = function() {},
      fBound = function() {
        return fToBind.apply(
          this instanceof fNOP && oThis ? this : oThis, //用new操作符调用绑定后的函数，返回的对象也能正常使用instanceof
          aArgs.concat(Array.prototype.slice.call(arguments))
        ); //把bind截取的参数与后面函数调用时传参合并
      }; //硬绑定函数是否是被 new 调用，如果是的话就会使用新创建的 this 替换硬绑定的 this。
    fNOP.prototype = this.prototype; //fToBind是拷贝对象
    fBound.prototype = new fNOP(); //给func/fBound拷贝一个FNOP的prototype
    //等同于 fBound.prototype = Object.create(this.prototype);
    return fBound;
  };
  //继承prototype--寄生组合式继承
}
```

<!--more-->

```js
function foo(a, b) {
  console.log("a:" + a + ", b:" + b);
}
foo.apply(null, [22, 33]); // a:22, b:33
var bar = foo.bind(null, 2); // 使用 bind(..) 进行柯里化
bar(3); // a:2, b:3
new bar(3); // a:2, b:3
```

#### 软绑定

- 背景：硬绑定大大降低函数的灵活性

```js
if (!Function.prototype.softBind) {
  Function.prototype.softBind = function(obj) {
    var fn = this;
    // 捕获所有 curried 参数
    var curried = [].slice.call(arguments, 1);
    var bound = function() {
      return fn.apply(
        !this || this === (window || global) ? obj : this,
        curried.concat.apply(curried, arguments)
      );
    };
    bound.prototype = Object.create(fn.prototype);
    return bound;
  };
}
//this 绑定到全局对象或者 undefined，则把指定的默认对象 obj 绑定到 this，否则不修改 this。

function foo() {
  console.log("name: " + this.name);
}
var obj = { name: "obj" },
  obj2 = { name: "obj2" },
  obj3 = { name: "obj3" },
  obj4 = {};
var fooOBJ = foo.softBind(obj);
fooOBJ(); // name: obj
obj2.foo = foo.softBind(obj);
obj2.foo(); // name: obj2 <---- 看！
fooOBJ.call(obj3); // name: obj3 <---- 看！
obj4.foo = foo.softBind(obj);
obj4.foo(); //name: undefined
setTimeout(obj2.foo, 10); // name: obj <---- 应用了软绑定
```
