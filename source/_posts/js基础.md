 ---
title: base
---

#### 字符串截取
> slice(start, end)
1. 支持负数, -1即最后一位 slice(0, -1) 截掉最后一位
> substring(start, end) 
1. 不支持负数
2. 两个参数中较小一个作为起始位置  substring(2, 4) = substring(4, 2)
> substr(start, len) 
#### Array.prototype.sort([compareFunction])
> 预设排序顺序：根据字串unicode编码
> compareFunction: 定义排序顺序
  1. compareFunction(a, b) 回传值小于0: a排在b前面
  2. (a, b) => a-b 升序
      cur: 10 next 3 10-3>0 交换位置变成3 10
  3. (a, b) => b-a 降序
      cur: 10 next 3 3-10<0 位置不变10 3
  4. 字符串排序
      (a, b) => a > b ? 1 : -1 字符串相减为NaN, 回传值必须为数字

#### toString
> 第二个参数：进制  
1. toSting(16) // 0-F
2. toSting(36) // 0-Z 

#### String.prototype.padEnd()
> 填充字符串以达到指定长度
1. str.padEnd(25, '.')
#### String.prototype.repeat()
> 重复字符串[MDN]
1. str.repeat(2)
```js
  function repeat(str, count) { 
    // 确保 count 为31位整数 绝大多数浏览器都不能支持 1 << 28 长的字符串，所以：
    if (str.length * count >= 1 << 28) {
      throw new RangeError('repeat count must not overflow maximum string size');
    }
    var rpt = '';
    for (;;) {
      if ((count & 1) === 1) {
        rpt += str;
      }
      count >>>= 1;  //0填充右位移
      if (count == 0) {
        break;
      }
      str += str;    //按2的指数相加
    }
    return rpt;
  }
```
//版本6：版本4的变样版本   免去创建数组与使用join方法   但在循环中创建字符串比要求的还长  所以
#### 数据类型
> null 和 undefined 只有文字形式,没有构造形式
> Date 只有构造,没有文字形式
### 二进制

> 所有的值都要转换成二进制，一个字符串由 0 个字符或者多个字符构成，它最终也要被转换成数值，再进一步被转换成二进制数值。
> 空字符串的值是''，最终还是要被转换成二进制的 0。
#### 原型作为默认值 
vals = vals || Array.prototype;  //Function.prototype空函数  
fn = fn || Function.prototype;  //RegExp.prototype空正则
rx = rx || RegExp.prototype;  //Array.prototype空数组


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


### Symbol 符号 简单标量基本类型 主要用于私有或特殊属性
```js
var sym = new Symbol("ss"); // TypeError 
var sym = Symbol("ss");

typeof sym; // "symbol" 原始类型值
var symObj = Object(sym);
typeof symObj; // "object"

Symbol.for("bar") === Symbol.for("bar"); // true
Symbol("bar") === Symbol("bar"); // false

Boolean(sym); // true  可转布尔值
Number(sym); // TypeError 不能转数值 

var s1 = Symbol.for([1, 2, 3]); //与Symbol的区别是:会被登记在全局环境中供搜索 不存在才新建值
Symbol.keyFor(s1); //返回一个已登记的 Symbol 类型值的key "1,2,3"


obj[Symbol("a")] = "a"; //不可枚举
obj[Symbol.for("b")] = "b";
obj["c"] = "c";
for (var i in obj) {
  console.log(i); // logs "c"
}

JSON.stringify({ [Symbol("foo")]: "foo" }); //stringify时被忽略

var sym = Symbol("foo");
var obj = { [sym]: 1 };
obj[sym]; // 1
obj[Object(sym)]; // still 1
 
Symbol.toStringTag  //内置 symbol, 对象的属性键
Object.definedPrototype(o, Symbol.toStringTag, {
  value: 'module' 
})
Object.prototype.toString.call(o) //"[object module]"
```
 

### code

````js 
// call  
Function.prototype.call = function(cxt, ...rest) {
  cxt = cxt || window 
  cxt.$fn = this  
  return cxt.$fn(...rest)
  delete cxt.$fn
}
```

