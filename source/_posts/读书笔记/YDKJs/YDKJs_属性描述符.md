---
title: 对象
categories: 
- [读书笔记, YDKJs]
---

#### 属性描述符

```js
var myObject = {};
Object.defineProperty(myObject, "a", {
  value: 2,
  writable: true, //可写
  configurable: true, //可配置
  enumerable: true //可枚举
});
myObject.a; // 2
```

<!--more-->

### 对象默认的 [[Put]] 和 [[Get]] 操作分别控制属性值的设置和获取。

#### [[Put]]

1. 属性是访问描述符？如果是并且存在 setter 就调用 setter。
2. 属性的数据描述符中 writable 是 false ？如果是，在非严格模式下静默失败，在
   严格模式下抛出 TypeError 异常。
3. 如果都不是，将该值设置为属性的值。

#### [[Get]]

在语言规范中，myObject.a 在 myObject 上实际上是实现了 [[Get]] 操作（有点像函数调
用：[[Get]]()）。对象默认的内置 [[Get]] 操作首先在对象中查找是否有名称相同的属性，
找到就返回属性值。如果没有找到同名属性，按照 [[Get]] 算法的定义沿着原型链查找

### getter 和 setter 改写默认操作，但只能应用在单个属性上

```js
Object.defineProperty(
  myObject, // 目标对象
  "b",{ // 属性名  // 描述符
    get: function(){ return this.a * 2 },  // 给 b 设置一个 getter
    enumerable: true  // 设置b出现在对象的属性列表中
  }）
var myObject = {
  get a() {  // 给 a 定义一个 getter
    return 2;
  }
};
myObject.a = 3;
myObject.a; // 2
  //由于只定义a的getter，对a的值进行设置时set操作会忽略赋值操作，不会抛出错误。set操作没有意义。
```

### **defineGetter**访问属性 和 **defineSetter**设置属性

```js
Date.prototype.__defineGetter__("ago", function() {
  return "ago";
});
var d = new Date();
d.ago; // 'ago'
```

<!--more-->

### 存在性

```js
var myObject = {
  a: 2
};
"a" in myObject; // true
myObject.hasOwnProperty("a"); // true
```

### @@iterator

```js
var myArray = [1, 2, 3];
var it = myArray[Symbol.iterator]();
it.next(); // { value:1, done:false }
it.next(); // { value:2, done:false }
it.next(); // { value:3, done:false }
it.next(); // { done:true }
//ES6 中的符号 Symbol.iterator 来获取对象的 @@iterator 内部属性。
//@@iterator 本身并不是一个迭代器对象，而是一个返回迭代器对象的函数
```

### 给任何想遍历的对象定义 @@iterator

```js
var myObject = {
  a: 2,
  b: 3
};
Object.defineProperty(myObject, Symbol.iterator, {
  enumerable: false,
  writable: false,
  configurable: true,
  value: function() {
    var o = this;
    var idx = 0;
    var ks = Object.keys(o);
    return {
      next: function() {
        return {
          value: o[ks[idx++]],
          done: idx > ks.length
        };
      }
    };
  }
});
// 手动遍历 myObject
var it = myObject[Symbol.iterator]();
it.next(); // { value:2, done:false }
it.next(); // { value:3, done:false }
it.next(); // { value:undefined, done:true }
//任何具有 Symbol.iterator 属性的元素都是可迭代的。
//for...of 语句创建一个循环来迭代可迭代的对象。
```


