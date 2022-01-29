---
title: 模块
categories: 
- [读书笔记, YDKJs]
---

#### 两个必要条件

> 1. 外部封闭函数，该函数至少被调用 1 次
> 2. 至少返回一个内部函数(在私有作用域种形成闭包)

#### 基本实现

```js
var foo = (function CoolModule() {
  var something = "cool";
  var another = [1, 3, 5];
  function doSomething() {
    console.log(something);
  }
  function doAnother() {
    console.log(another.join(" ! "));
  }
  return {
    doSomething: doSomething,
    doAnother: doAnother
  };
})();
//立即调用函数，返回值赋给单例的模块实例foo
```

<!--more-->

#### API 封装

```js
  var MyModule = (function Manager() {
    var modules = {};
    function define(name, deps, impl) {
      for (var i=0; i<deps.length; i++) {
        deps[i] = modules[deps[i]];   //依赖的函数 按序放入参数
      }
      modules[name] = impl.apply( impl, deps );
    }
    function getModules(name) {
      return modules[name];
    }
    return {
      define: define,
      getModules: getModules
    };
    })();
  })()
  //核心 模块API 根据名字存储 modules[name] = impl.apply(impl, deps)
  //依赖的函数 作为参数
```

```js
MyModules.define("bar", [], function() {
  function hello(who) {
    return "Let me introduce: " + who;
  }
  return {
    hello: hello
  };
});
MyModules.define("foo", ["bar"], function(b) {
  var hungry = "leaves";
  function awesome() {
    console.log(b.hello(hungry).toUpperCase());
  }
  return {
    awesome: awesome
  };
});
MyModules.define("zoo", ["bar", "foo"], function(b, f) {
  function cool() {
    console.log(f.awesome());
  }
  return {
    cool: cool
  };
});
var bar = MyModules.getModules("bar");
var foo = MyModules.getModules("foo");
var zoo = MyModules.getModules("zoo");
console.log(bar.hello("hippo")); // Let me introduce: hippo
foo.awesome(); // LET ME INTRODUCE: LEAVES
zoo.cool(); // LET ME INTRODUCE: LEAVES
```
