---
title: 异步
categories: 
- [读书笔记, YDKJs]
---

#### 链式流程 Promise 固有特性

> function reject(){} 处理异步拒绝的决议 catch 能捕获包括语法错误在内的所有错误
> 调用 Promise 的 then(..) 会自动创建一个新的 Promise 从调用返回。
> 在完成或拒绝处理函数内部，如果返回一个值或抛出一个异常，新返回的 Promise 就相应地决议。
> 如果完成或拒绝处理函数返回一个 Promise，会成为当前 then(..) 返回的链接 Promise 决议值。

<!--more-->

#### Promise.all([ .. ]) Promise.race([ .. ])

> Promise.all([ .. ])同时执行两个或更多步骤，且不在乎顺序
> Promise.race([ .. ])两个或更多步骤竞争，返回最快的结果
> 如果 promise 中任一被拒绝，主 Promise.all([ .. ])或 Promise.race([ .. ])立即被拒绝，并丢弃来自其他所有 promise 的全部结果。

#### then 和 catch

> then(..) 接受一个或两个参数:用于成功及拒绝回调。如果两者中 的任何一个被省略或者作为非函数值传入的话，就会替换为相应的默认回调。
> 默认完成回调会把消息传递下去，而默认拒绝回调则抛出(传播)其接收到的出错原因。
> catch(..) 参数是一个拒绝回调，并自动替换默认完成回调。等价于 then(null,..)。
> 两者都会创建并返回一个新的 promise
> 如果任意一个回调返回非 Promise、非 thenable 的立即值，这个值会被用作返回 promise 的完成值。
> 如果完成处理函数返回一个 promise 或 thenable，那么这个值会被展开，并作为返回 promise 的决议值。

#### Promise

>

```js
var p = Promise.resolve(2);
p.then(function(v) {
  //区别一  return
  return v + v;
}).then(function(v) {
  console.log(v); //4
});
p.then(function(v) {
  //区别二
  setTimeout(() => {
    return v + v;
  });
}).then(function(v) {
  console.log(v); //undefined
});
p.then(function(v) {
  //区别三  resolve
  return new Promise(function(resolve, reject) {
    setTimeout(() => {
      resolve(v * 100);
    }, 300);
  });
}).then(function(v) {
  console.log(v); //200
});

var p = (function() {
  return new Promise(function(res, rej) {
    console.log("init");
    res("hello");
  });
})();
p.then(v => {
  console.log(v);
});
console.log("world");
//init
//world
//hello
//严格地说，交互过程中有两个 promise：300ms 延迟 promise，和第二个 then(..) 链接到的那个链接 promise。
//Promise 机制已经自动把它们的状态合并在了一起。300ms 延迟 promise创建了一个 promise，并用其替换了前面返回的链接 promise。
```

#### 生成器+ Promise

```js
function foo(x, y) {
  return request("http://some.url.1/?x=" + x + "&y=" + y);
}
function* main() {
  try {
    var text = yield foo(11, 31);
    console.log(text);
  } catch (err) {
    console.error(err);
  }
}
var it = main();
var p = it.next().value; //接收和连接yield出来的promise
// 等待promise p决议
p.then(() => {});
```

#### 对比以下

```js
//一
function bar() {
  Promise.all( [
  baz( .. )
  .then( .. ),
  Promise.race( [ .. ] )
  ] )
  .then( .. )
}
//二
function bar(url1,url2) {
  return Promise.all( [
  request( url1 ),
  request( url2 )
  ] );
}
function *foo() {
 var results = yield bar(  // 隐藏bar(..)内部基于Promise的并发细节
 "http://some.url.1",
 "http://some.url.2"
 );
 var r1 = results[0];
 var r2 = results[1];
 var r3 = yield request( "http://some.url.3/?v=" + r1 + "," + r2 );
 console.log( r3 );
}
// 使用前面定义的工具run(..)
run( foo );
// 第二种生成器+Promise 异步代码，相比于其他实现，这种抽象更加简洁。
```

#### async 与 await

```js
function foo(x, y) {
  return request("http://some.url.1/?x=" + x + "&y=" + y);
}
async function main() {
  try {
    var text = await foo(11, 31);
  } catch (err) {}
  u;
}
main();
//async函数 用await等promise决议
```

#### 形式程序

```js
function foo(x, y, cb) {
  setTimeout(function() {
    cb(x + y);
  }, 1000);
}
function fooThunk(cb) {
  // 手动
  foo(3, 4, cb);
}
fooThunk(function(sum) {
  console.log(sum); // 7
});
// 封装工作
function thunkify(fn) {
  var args = [].slice.call(arguments, 1);
  return function(cb) {
    args.push(cb);
    return fn.apply(null, args);
  };
}
var fooThunk = thunkify(foo, 3, 4);
fooThunk(function(sum) {
  console.log(sum); // 7
});
// 进一步
function thunkify(fn) {
  return function() {
    var args = [].slice.call(arguments);
    return function(cb) {
      args.push(cb);
      return fn.apply(null, args);
    };
  };
}
var whatIsThis = thunkify(foo); //两个独立步骤保留一个更清晰的功能分离
var fooThunk = whatIsThis(3, 4);
fooThunk(function(sum) {
  console.log(sum); // 7
});
```

#### 扩展 API

```js
//返回完成的Promise,忽略拒绝
Promise.any = function(prs) {
  const result = [];
  return Promise.all(
    prs.map(item => {
      return Promise.resolve(item).then(
        res => {
          result.push(res);
        },
        () => {}
      );
    })
  ).then(() => {
    return new Promise((res, rej) => {
      if (result.length > 0) res(result);
      else rej();
    });
  });
};

//竞态any,第一个promise完成即完成
Promise.first = function(prs) {
  return new Promise((res, rej) => {
    let rejNum = 0;
    prs.forEach(pr => {
      Promise.resolve(pr).then(res, () => {
        if (++rejNum == prs.length) {
          rej();
        }
      });
    });
  });
};

//所有拒绝的promise
Promise.none = function(prs) {
  const errArr = [];
  return Promise.all(
    prs.map(item => {
      return new Promise((res, rej) => {
        //primise.all里所有promise实例反过来
        return Promise.resolve(promise).then(rej, res);
      });
    })
  );
};
```

#### 手动实现 promise

```js
```
