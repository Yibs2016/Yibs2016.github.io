---
title: 来看看装逼的js
---

### 过去七天的数组

```js
[...Array(7).keys()].map(days => new Date(Date.now() - 86400000 * days));
```

### 随机 ID

```js
Math.random().toString(36).substring(2);   // hg7znok52x

<!--more-->

```

### 获取 URL 的查询参数

```js
// ?foo=bar&baz=bing => {foo: bar, baz: bing}
q = {};
location.search.replace(/([^?&=]+)=([^&]+)/g, (_, k, v) => (q[k] = v));
// [^?&=] 非? 非& 非=
function compress(source) {
  var keys = {};
  source.replace(/([^=&]+)=([^&]*)/g, function(full, key, value) {
    keys[key] = (keys[key] ? keys[key] + "," : "") + value;
  });
  var result = [];
  for (var key in keys) {
    result.push(key + "=" + keys[key]);
  }
  return result.join("&");
}
console.log(compress("a=1&b=2&a=3&b=4")); // => "a=1,3&b=2,4"
```

### 数组混淆

```js
arr => arr.slice().sort(() => Math.random() - 0.5);
```

### 生成随机十六进制代码（生成随机颜色）

```js
"#" +
  Math.floor(Math.random() * 0xffffff)
    .toString(16)
    .padEnd(6, "0");
//padStart()头部补全 padEnd()尾部补全
//'x'.padStart(5, 'abc') // 'abcax' 'x'.padEnd(5, 'ab') // 'xabab'
```

<!--more-->

### 数组去重 创建特定大小的数组 重复字符串

```js
[...new Set(arr)]
[...Array(3).keys()] //[0, 1, 2]
new Array(n+1).join(str)
```

### 命名空间

```js
var GLOBAL = {};
GLOBAL.nameSpace = function(str) {
  var arr = str.split("."),
    o = GLOBAL;
  for (var i = arr[0] == "GLOBAL" ? 1 : 0; i < arr.length; i++) {
    o[arr[i]] = o[arr[i]] || {};
    o = o[arr[i]];
  }
};
```

### Symbol 符号 一种简单标量基本类型 主要用于私有或特殊属性

> 生成的 Symbol 是一个原始类型的值，不是对象

```js
var sym = new Symbol("ss"); // TypeError
var sym2 = new Symbol("ss"); // TypeError
var sym = Symbol("ss");
typeof sym; // "symbol"
var symObj = Object(sym);
typeof symObj; // "object"
ymbol.for("bar") === Symbol.for("bar"); // true
Symbol("bar") === Symbol("bar"); // false
//Symbol值可转布尔值，但不能转数值。
Boolean(sym); // true
Number(sym); // TypeError

var s1 = Symbol.for([1, 2, 3]); //与Symbol的区别是:会被登记在全局环境中供搜索 不存在才新建值
Symbol.keyFor(s1); //返回一个已登记的 Symbol 类型值的key "1,2,3"
var symf = Symbol.for(arr1);
Symbol.keyFor(symf);

obj[Symbol("a")] = "a"; //不可枚举
obj[Symbol.for("b")] = "b";
obj["c"] = "c";
for (var i in obj) {
  console.log(i); // logs "c"
}
JSON.stringify({ [Symbol("foo")]: "foo" }); //被忽略

var sym = Symbol("foo");
var obj = { [sym]: 1 };
obj[sym]; // 1
obj[Object(sym)]; // still 1

//**使用：配合原型方法的一些开关，强化方法的使用。
typeof Symbol.iterator === "symbol";
typeof Symbol.hasInstance === "symbol";
//Symbol.isConcatSpreadable
//Symbol.toStringTag
//Symbol.unscopables
```

### 显式混入

```js
function mixin(sourceObj, targetObj) {
  for (var key in sourceObj) {
    // 只会在不存在的情况下复制
    if (!(key in targetObj)) {
      targetObj[key] = sourceObj[key];
    }
  }
  return targetObj;
}
//由于两个对象引用的是同一个函数，因此这种复制（或者说混入）并不能完全模拟面向类的语言中的复制。
```

### 隐式混入

```js
var Something = {
  cool: function() {
    this.greeting = "Hello World";
    this.count = this.count ? this.count + 1 : 1;
  }
};
Something.cool();
Something.greeting; // "Hello World"
Something.count; // 1
var Another = {
  cool: function() {
    // 隐式把 Something 混入 Another
    Something.cool.call(this);
  }
};
Another.cool();
Another.greeting; // "Hello World"
Another.count; // 1（count 不是共享状态）
//Something 的行为“混入”到了 Another 中。
```

### 补充知识点

> null 和 undefined 只有文字形式,没有构造形式
> Date 只有构造,没有文字形式

> 对象属性可通过. =>"属性访问" 或 [] =>"键访问"
> 如果要引用 "Super-Fun!" 属性，必须用 ["Super-Fun!"] 语法访问，因为 Super-Fun! 并不是一个有效的标识符属性名。

> 在对象中，属性名永远都是字符串。
> myObject[myObject] = "baz";  
> myObject["[object Object]"]; //baz

### 函数

- 函数是“可调用对象”，它的一个内部属性 [[Call]]，该属性使其可以被调用。

```js
function a(b, c) {}
a.length; //2
```

### 匿名函数

> 调试栈更难追踪;
> 自我引用(递归、事件(解除)绑定，等等)更难;
> 代码(稍微)更难理解。

### typeof 安全防范机制 通过 typeof (阻止报错)来检查 undeclared 变量

```js
//检查 DEBUG 变量是否已被声明
if (DEBUG) {
  // 会抛出错误
  console.log("Debugging is starting");
}
if (typeof DEBUG !== "undefined") {
  // 这样才安全
  console.log("Debugging is starting");
}
```

### js 没有 else if

> if()else if() 事实上是 if()else { if() else}

### void 运算符不让表达式返回任何结果

> return void setTimeout( doSomething,100 );

### 宽相等 严相等

> == 允许在相等比较中进行强制类型转换，而 === 不允许

### +

> 不是字符串=>强制转为字符串=>拼接

```js
var a = [1, 2];
var b = [3, 4];
a + b; // "1,23,4"   ... '1,2'+'3,4'
```

### 常量形式

> 强烈建议使用常量形式(如 /^a\*b+/g)来定义正则表达式
> 常量形式比 new RegExp( "\\b(?:" + name + ")+\\b", "ig" )执行效率更高

### 将原型作为默认值

```js
//Function.prototype空函数，RegExp.prototype“空”的正则表达式(无任何匹配)，Array.prototype 空数组。
vals = vals || Array.prototype;
fn = fn || Function.prototype;
rx = rx || RegExp.prototype;
// .prototypes 已被创建并且仅创建一次。相反，如果将 []、function(){} 和 /(?:)/ 作为默认值，则每次调用都会被重新创建一次 (具体创建与否取决于 JavaScript 引擎，稍后它们可能会被垃圾回收)，造成内存和 CPU 资源的浪费。
```

### super

> 看作只与类相关
> 只允许以 super.XXX 的形式(用于属性 / 方法访问)出现，而不能以 super() 的形式出现。

```js
var o1 = {
  foo() {
    console.log("o1:foo");
  }
};
var o2 = {
  foo() {
    super.foo(); //锁定到 o2 的 [[Prototype]] = Object.getPrototypeOf(o2)
    console.log(" ");
  }
};
Object.setPrototypeOf(o2, o1);
o2.foo(); // o1:foo o2:foo
```

### 错误处理

> try..catch 只能捕获同步错误，无法用于异步代码模式：

### ES5 展开参数

```js
function spread(fn) {
  return Function.apply.bind(fn, null);
}
spread(function(x, y) {
  console.log(x, y); //1 2
})([1, 2]);
```

### 运算符 优先级与关联

> & | 优先级高于 ?
> & | 左关联(左到右组合) ?右关联(右到左组合)

### 二进制

> 所有的值都要转换成二进制，一个字符串由 0 个字符或者多个字符构成，它最终也要被转换成数值，再进一步被转换成二进制数值。
> 空字符串的值是''，最终还是要被转换成二进制的 0。

### 编码

#### ASCII 码

> 英语字符与二进制位之间的关系，这被称为 ASCII 码，ASCII 码一共规定了 128 个字符的编码

#### 非 ASCII 码

> 英语用 128 个符号编码就够了，但是用来表示其他语言，128 个符号是不够的。

#### Unicode

> 为每种语言中的每个字符设定统一唯一的二进制编码，以满足跨语言、跨平台进行文本转换、处理的要求。
> var reg = /^([\u4E00-\u9FA5])\*\$/; 中文编码的开始和结束值
> 它只规定了符号的二进制代码，却没有规定这个二进制代码应该如何存储。

#### UTF-8

> UTF-8 就是在互联网上使用最广的一种 Unicode 的实现方式。
> 是一种变长的编码方式。可以使用 1~4 个字节表示一个符号，根据不同的符号而变化字节长度。

### 精准校验数据类型

````js
var utils = {};
"Boolean|Number|String|Function|Array|Date|RegExp|Object|Error".split("|").forEach(fun
ction (item) {
  utils["is" + item] = function (obj) {
  return {}.toString.call(obj) == "[object " + item + "]";
  };
});
utils.isArray([1, 2, 3])     // 输出：true

for ( var i = 0, type; type = [ 'String', 'Array', 'Number' ][ i++ ]; ){
    (function( type ){
        Type[ 'is' + type ] = function( obj ){
            return Object.prototype.toString.call( obj ) === '[object '+ type +']';
        }
       })( type )
};

Type.isArray( [] );     // 输出：true
Type.isString( "str" );     // 输出：true

### 创建一个快速的解绑定包装器
```js\
//让Foo.method跟Foo.prototype.method完成一样的功能，但是想显式地指定方法体中的this对象（通过第一个参数），而不是Foo.prototype.method本身绑定的。
function Foo() {}
Foo.prototype.method = function(a, b, c) {
    console.log(this, a, b, c);
};
Foo.method = function() {
    // 结果: Foo.prototype.method.call(this, arg1, arg2... argN)
    Function.call.apply(Foo.prototype.method, arguments);
};
Foo.prototype.method.call(context, arg0, arg1, ...);
//但是arg0等参数列表是不定长的
call方法.apply(Foo.prototype.method, arguments);
//以Foo.prototype.method为作用对象调用一个call方法，以Foo.method的arguments为参数。
//1以method为作用对象调用call方法(指定调用者为this, 即method)
//2对于call方法，this才是执行的方法，因此等同于 method.call(someThis, arg1, arg2, ...)
//3method是调用call时的调用对象，即call执行时的this, 而传入的someThis是作为method执行时的this使用的

````
