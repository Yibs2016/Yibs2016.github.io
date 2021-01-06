---
title: ArrayBuffer对象
---

#### 概述

> ArrayBuffer 对象代表储存二进制数据的一段内存，它不能直接读写，只能通过视图（TypedArray 视图和 DataView 视图)来读写，视图的作用是以指定格式解读二进制数据。
> ArrayBuffer 一个构造函数，可以分配一段可以存放数据的连续内存区域。

```js
var buf = new ArrayBuffer(32);
//生成一段32字节内存区域，每个字节值默认0。
```

#### 读写方式一 DataView 视图

```js
var buf = new ArrayBuffer(32);
var dataView = new DataView(buf);
dataView.getUint8(0); // 0
```

#### 读写方式二 TypedArray 视图

```js
var buffer = new ArrayBuffer(12);
var x1 = new Int32Array(buffer);
x1[0] = 1;
var x2 = new Uint8Array(buffer);
x2[0] = 2;
x1[0]; // 2
//建立两种视图：32位带符号整数和8位不带符号整数。两个视图对应同一段内存，一个视图修改底层内存，影响到另一个视图。
```

<!--more-->

#### ArrayBuffer.prototype.byteLength

> 返回所分配内存区域字节长度

```js
var buffer = new ArrayBuffer(32);
buffer.byteLength; // 32
```

#### ArrayBuffer.prototype.slice()

> 将一部分内存区域，拷贝生成一个新 ArrayBuffer 对象。

```js
var buffer = new ArrayBuffer(8);
var newBuffer = buffer.slice(0, 3);
```

#### ArrayBuffer.isView()

> 是否为 TypedArray 实例或 DataView 实例

```js
var buffer = new ArrayBuffer(8);
ArrayBuffer.isView(buffer); // false
var v = new Int32Array(buffer);
ArrayBuffer.isView(v); // true
```

#### TypedArray

> TypedArray 视图一共包括 9 种类型，每一种视图都是一种构造函数。
> Int8Array Uint8Array Uint8ClampedArray Int16Array Uint16Array Int32Array Uint32Array Float32Array Float64Array
> TypedArray 只是一层视图，本身不储存数据，它的数据都储存在底层的 ArrayBuffer 对象之中，要获取底层对象必须使用 buffer 属性。

#### TypedArray 也可以接收普通数组

> 直接分配内存生成底层 ArrayBuffer 实例，同时为这段内存赋值

```js
var typedArray = new Uint8Array([0, 1, 2]);
typedArray.length; // 3
typedArray[0] = 5;
typedArray; // [5, 1, 2]
```
