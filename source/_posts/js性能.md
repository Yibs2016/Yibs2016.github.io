---
title: 性能
---

### 程序性能

> 对 JavaScript 性能影响最大的因素是内存分配、垃圾收集和作用域访问。

#### Web Worker

> 在独立的线程运行一个 JavaScript 文件（即程序），使用异步事件在
> 线程之间传递消息。它们非常适用于把长时间的或资源密集型的任务卸载到不同的线程中，以提高主 UI 线程的响应性。
> 应用场景
> 处理密集型数学计算
> 大数据集排序
> 数据处理（压缩、音频分析、图像处理等）
> 高流量网络通信

##### 主线程

```js
var worker = new Worker("task.js");
worker.postMessage(); //主线程向Worker发消息
worker.onmessage = function(event) {
  //接收子线程发回的消息
  console.log("Received message " + event.data); //事件对象的data属性
  doSomething();
};
worker.terminate(); //关闭Worker
```

<!--more-->

##### Worker 线程

```js
self.addEventListener(
  "message",
  function(e) {
    //监听
    self.postMessage("You said: " + e.data);
  },
  false
);
self.postMessage("Worker started: "); //发送
importScripts("script1.js", "script2.js"); //加载其他脚本
worker.addEventListener("error", function(event) {
  // 触发主线程error事件
});
self.close(); //关闭线程
```

##### asm.js

> 避免了 JavaScript 难以优化的部分（比如垃圾收集和强制类型转换），并且让 JavaScript 引擎识别并通过激进的优化运行代码。
> 1 类型和强制类型转换
> 2 声明一个堆（heap = new ArrayBuffer( 0x10000 )）(// 比如 64k 堆)并将其传入 asm.js 模块，asm.js 模块就不需要任何可能导致内存扰动的动作，只需使用预先保留的空间即可。

##### 尾调用优化[TCO ]

> ES6 要求引擎实现 TCO。允许一个函数在结尾处调用另外一个函数来执行，不需要任何额外资源。对递归算法来说，引擎不再需要限制栈深度。
> 首先，调用一个函数需要需要额外一块预留内存来管理调用栈
> 然而，支持 TCO 的引擎能意识到函数位于尾部，表明主函数基本完成
> 那么，在调用另外一个函数时，不需再创建新的栈帧(而是复用已有的)
> 从而，速度更快，节省内存

```js
function factorial(n) {
  //TCO 友好
  function fact(n, res) {
    if (n < 2) return res;
    return fact(n - 1, n * res);
  }
  return fact(n, 1);
}
factorial(5); // 120
```

#### 性能测试

```js
var start = new Date().getTime(); // 或者Date.now()
// 进行一些操作
var end = new Date().getTime();
console.log("Duration:", end - start);
//这种方案有很多错误
//1报告时间是 0的情况，不精确
//这个运算的这次特定的运行消耗了大概这么长时间。而它并不总是以这样的速度运行，你不知道引擎或系统在这个时候有没有受到什么影响，以及其他时候这个运算会不会运行得更快。
```

#### 工具

> Benchmark.js 公平、可靠、有效的性能测试
> jsPerf 采集 Benchmark.js 库运行的测试结果

#### 函数节流

```js
var throttle = function(fn, itv){
  var self = fn,
    timer, firstTime = true;
  return functon(){
    var args = arguments,
    _me = this;
    if(firstTime){
      self.apply(_me,args);
      return firstTime = false;
    }
    if(timer){
      return false;
    }
    timer = setTimeout(function(){
      clearTimeout(timer);
      timer = null;
      self.apply(_me, args);
    }, itv || 500);
  }
}
window.onresize = throttle(function(){
    console.log( 1 );
}, 500 );
```

#### 函数分时

```js
var timeChunk = function(ary, fn, count) {
  var obj, t;
  var len = ary.length;
  var start = function() {
    for (var i = 0; i < Math.min(count || 1, ary.length); i++) {
      var obj = ary.shift();
      fn(obj);
    }
  };
  return function() {
    t = setInterval(function() {
      if (ary.length === 0) {
        // 如果全部节点都已经被创建好
        return clearInterval(t);
      }
      start();
    }, 200); // 分批执行的时间间隔，也可以用参数的形式传入
  };
};
```

#### 函数防抖

```js
var debounce = function(fn, interval = 300) {
  let timer = null;
  return function() {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, arguments);
    }, interval);
  };
};
debounce(function() {
  console.log("bindInputDebounce");
});
```
