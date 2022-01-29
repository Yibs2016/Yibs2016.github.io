---
title: 生成器
categories: 
- [读书笔记, YDKJs]
---

#### 生成器就是一类特殊的函数

> 可以一次或多次启动和停止

```js
var x = 1;
function* foo() {
  x++;
  yield; // 暂停!
  console.log("x:", x);
}
//指示生成器 *foo(..) 从当前位置开始运行，停在下一个 yield 处或者直到生成器结束。
function* foo(x, y) {
  return x * y;
}
var it = foo(6, 7); //创建了一个迭代器对象
```

<!--more-->

#### 迭代消息传递

```js
function* foo(x) {
  var y = x * (yield);
  return y;
}
var it = foo(6);
// 启动foo(..) 在在赋值语句中间暂停 *foo(..)
it.next();
var res = it.next(7); //把7传回作为被暂停的 yield 表达式的结果。
res.value; // 42
```

#### yield 跟 next 双向消息传递系统

> yield.. 作为一个 表达式可以发出消息响应 next(..) 调用，next(..) 也可以向暂停的 yield 表达式发送值。

```js
function* foo(x) {
  var y = x * (yield "Hello"); // <-- yield一个值!
  return y;
}
var it = foo(6);
var res = it.next(); // 第一个next()，并不传入任何东西 "
res.value; // "Hello
res = it.next(7); // 向等待的yield传入7
res.value; // 42
```

#### step(..) 的辅助函数，控制迭代器

```js
function step(gen) {
  var it = gen();
  var last;
  return function() {
    last = it.next(last).value; // 不管yield出来的是什么，下一次都把它原样传回去!
  };
}
```

#### Symbol.iterator

> ES6 预定义的特殊 Symbol 值之一
> for..of 循环在每次迭代中自动调用 next()
> 调用这个函数时，它会返回一个迭代器

#### 生成器委托

```js
//A
function *foo() {
 var r2 = yield request( "http://some.url.2" );
 var r3 = yield request( "http://some.url.3/?v=" + r2;
 return r3;
}
function *bar() {
 var r1 = yield request( "http://some.url.1" );
 var r3 = yield run( foo ); // 通过 run(..) "委托"给*foo()
 console.log( r3 );
}
run( bar );
//B
function *bar() {
 yield 1;
 yield *foo(); // yield委托！
 yield 5;
}
```

#### 生成器并发

```js
var res = [];
function* reqData(url) {
  res.push(yield request(url));
}
var it1 = reqData("http://some.url.1");
var it2 = reqData("http://some.url.2");
var p1 = it1.next(); //启动请求
var p2 = it2.next();
//A
p1.then(function(data) {
  it1.next(data); //往*reqData yield回传异步结果 res[0]
  return p2;
}).then(function(data) {
  it2.next(data); //往*reqData yield回传异步结果 res[1]
});
//在 p1 决议时恢复第一个实例，然后 p2 的决议重启第二个实例。
//B
function* reqData(url) {
  var data = yield request(url);
  yield; // 控制转移
  res.push(data);
}
p1.then(function(data) {
  it1.next(data);
});
p2.then(function(data) {
  it2.next(data);
});
Promise.all([p1, p2]) //在 Promise.all([ .. ]) 处理函数中选择它们的恢复顺序
  .then(function() {
    it1.next();
    it2.next();
  });
```
