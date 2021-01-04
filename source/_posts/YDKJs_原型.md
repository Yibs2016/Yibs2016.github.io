---
title: [[Prototype]]
---

#### [[Prototype]]

> js 对象的内置属性
> 对于默认的[[Get]]，如在未在对象本身找到对应的属性，就会继续访问对象的[[Prototype]]链

##### Object.prototype

> 所有普通[[Prototype]]链尽头是内置 Object.prototype，包含很多功能，如: .toString(),.valueOf(), .hasOwnProperty(..)等。

<!--more-->

##### 属性设置和屏蔽

- [[Prototype]] 链上<u>不存在</u>该数据访问属性，直接设置
- [[Prototype]] 链上<u>存在</u>
  > 1. 屏蔽属性->[[Prototype]] 链上的数据访问属性 foo 没被标记为只读（writable:false），就直接在 myObject 中添加一个名为 foo 的新属性
  > 2. 屏蔽属性->[[Prototype]] 链上被标记为只读，无法修改已有属性或创建屏蔽属性,赋值语句会被忽略。严格模式下报错
  > 3. [[Prototype]] 链上层存在 foo 并且它是一个 setter，就一定会调用这个 setter。foo 不会被添加到 myObject，也不会重新定义 foo 这个 setter

```js
let data1 = {
  name: "小明",
  age: 18
};
Object.defineProperty(data1, "age", {
  set: function(newAge) {
    console.log(this.name + "现在" + newAge + "岁");
  },
  get: function() {
    return 18;
  }
});
var person2 = Object.create(data1); //person2 {}
person2.age = 16; //person2 {}
person2.age; //18
person2.name = "Yibs"; //person2 {name: 'Yibs'}
```

- 如果要在这情况下也屏蔽 age，可使用 Object.defineProperty(..)向 person2 添加 age

#### 关联 委托

```js
var a = new Foo();
Object.getPrototypeOf(a) === Foo.prototype; // true
//并没有从“类”中复制任何行为到一个对象中，只是让两个对象互相关联。
//绝大多数 js 开发者不知道的秘密是，new Foo() 这个函数调用实际上并没有直接创建关联，这个关联只是一个意外的副作用。new Foo() 只是间接完成了目标：一个关联到其他对象的新对象。
//更直接的方法是 Object.create(..)
//JavaScript 会在两个对象之间创建一个关联，这样一个对象就可以通过委托访问另一个对象的属性和函数。
```

#### [new](http://www.cnblogs.com/onepixel/p/5043523.html) 与 [create](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

> new 生成的对象 内部链接[[Prototype]]关联到构造函数的原型
> Object.create()创建新对象,该对象继承指定原型对象的属性和方法

```js
function Foo(){}
var a = new Foo();
Object.getPrototypeOf(a) === Foo.prototype; //true
<br>
function obj(o) {
    function Fn() {};
    Fn.prototype = o;
    return new Fn();
}
var person = {
    job: "IT"
}
var p1 = obj(person);
var p2 = Object.create(person);
console.log(p1.job == p2.job);   //true
```

#### 构造函数

> function Foo() {
> // ...
> }
> Foo.prototype.constructor === Foo; // true
> var a = new Foo();
> a.constructor === Foo; // true constructor 指向“创建这个对象的函数”。
> 实际上 a 本身并没有 .constructor 属性。虽然 a.constructor 确实指向 Foo 函数，但是并不表示 a 由 Foo“构造”
> 哈哈 =>> a.constructor 只是通过默认的 <u>[[Prototype]] 委托</u>指向 Foo，
> !!!函数不是构造函数，但是当且仅当使用 new 时，函数调用会变成“构造函数调用”。

#### 修复 .constructor

```js
function Foo() {
  /* .. */
}
Foo.prototype = {
  /* .. */
}; // 创建一个新原型对象  // 需要在 Foo.prototype 上“修复”丢失的 .constructor 属性
var a1 = new Foo();
a1.constructor === Foo; // false!
a1.constructor === Object; // true!
//a1 并没有 .constructor 属性，所以会委托 [[Prototype]] 链上的 Foo.prototype。但是这个对象也没有 .constructor 属性（默认的 Foo.prototype 对象会有这个属性！），所以它会继续委托，这次会委托给委托链顶端的 Object.prototype。这个对象有 .constructor 属性，指向内置的 Object(..) 函数。

Foo.prototype.constructor = Foo; //方法一
Object.defineProperty(Foo.prototype, "constructor", {
  //方法二
  enumerable: false,
  writable: true,
  configurable: true,
  value: Foo // 让 .constructor 指向 Foo
});
```

#### 原型继承

```js
Bar.prototype = Foo.prototype ;
// 并不会创建一个关联到 Bar.prototype 的新对象，只是让 Bar.prototype 直接引用 Foo.prototype 对象。因此当你执行类似 Bar.prototype.myLabel = ... 的赋值语句时会直接修改 Foo.prototype 对象本身。
Bar.prototype = new Foo();
// 基本上满足你的需求，但是可能会产生一些副作用 :(
// 如果函数 Foo 有一些副作用（比如写日志、修改状态、注册到其他对象、给 this 添加数据属性，等等）的话，就会影响到 Bar() 的“后代”
Object.create(..);
// 创建一个合适的关联对象
// 这样做唯一的缺点就是需要创建一个新对象然后把旧对象抛弃掉，不能直接修改已有的默认对象。
// ES6 之前需要抛弃默认的 Bar.prototype
Bar.ptototype = Object.create( Foo.prototype );
// ES6 辅助函数 Object.setPrototypeOf(..) 开始可以直接修改现有的 Bar.prototype
Object.setPrototypeOf( Bar.prototype, Foo.prototype );
```

#### 检查“类”关系

> 检查一个实例（JavaScript 中的对象）的继承祖先（JavaScript 中的委托关联）通常被称为
> 内省（或者反射）。

```js
a instanceof Foo; // true
//在 a 的整条 [[Prototype]] 链中是否有指向 Foo.prototype 的对象
Foo.prototype.isPrototypeOf(a); // true
// b 是否出现在 c 的 [[Prototype]] 链中？
b.isPrototypeOf(c);
//获取一个对象的 [[Prototype]] 链
Object.getPrototypeOf(a);
```

#### .**proto**

> 实际上并不存在于你正在使用的对象中
> 和其他的常用函数（.toString()、.isPrototypeOf(..)，等等）一样，存在于内置的 Object.prototype 中。

```js
Object.defineProperty(Object.prototype, "__proto__", {
  get: function() {
    return Object.getPrototypeOf(this); //获取对象的 [[Prototype]] 链
  },
  set: function(o) {
    // ES6 中的 setPrototypeOf(..)
    Object.setPrototypeOf(this, o);
    return o;
  }
});
```

#### 对象关联

```js
//Object.create第二个参数指定需要添加到新对象中key跟value
var anotherObject = {
  a: 2
};
var myObject = Object.create(anotherObject, {
  b: {
    enumerable: false,
    writable: true,
    configurable: false,
    value: 3
  }
});
myObject.hasOwnProperty("a"); // false
myObject.hasOwnProperty("b"); // true
myObject.a; // 2
myObject.b; // 3
// polyfill 但无法模拟第二个参数的功能
if (!Object.create) {
  Object.create = function(o) {
    function F() {}
    F.prototype = o;
    return new F();
  };
}
```

#### 对比以下

```js
var anotherObject = {
  cool: function() {
    console.log("cool!");
  }
};
var myObject = Object.create(anotherObject);
myObject.cool(); // "cool!"
//难以理解和维护
var anotherObject = {
  cool: function() {
    console.log("cool!");
  }
};
var myObject = Object.create(anotherObject);
myObject.doCool = function() {
  this.cool(); // 内部委托！
};
myObject.doCool(); // "cool!"
//API 设计更加清晰
```

#### new 带参数的优先级 高于函数调用

> new Foo() => new Foo ? 还是 Foo()后再 new?

```js
function inheirt(o) {
  function ff() {}
  ff.prototype = o;
  console.log(this);
  return new ff();
}
var ex1 = inheirt(o); //window
var ex2 = new inheirt(o); //inheirt{}  ==>先new一个对象
```

> Foo()函数有返回值的情况 实例结果?

```js
var fun = function() {
  this.name = "peter";
  return {
    //返回值是一个引用类型
    name: "jack"
  };
};
fun.prototype.foo = function() {
  console.log("foo");
};
var p = new fun(); //==>先new一个对象 再调用函数
p.name; //jack    p为一个对象 {name: 'jack'};
p.foo(); //p.foo is not a function

var fun = function() {
  this.name = "peter";
  return "jack"; //返回值是一个普通类型
};
var p = new fun();
p.name; //peter    p为构造函数实例对象 忽略返回值
p.foo(); //foo
```

> 总结: 函数返回原始变量 new 返回函数的实例 this 函数返回一个 object、array 或 function 则会覆盖 this 实例
