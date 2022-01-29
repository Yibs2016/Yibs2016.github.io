---
title: js常用工具函数
---
 
### 格式化时间
```js
function formatTime(val, tmpl='{0}年{1}月{2}日{3}时{4}分{5}秒') {
  let timeArr = val.match(/\d+/g)
  return tmpl.replace(/\{(\d+)\}/g, (...[, $1]) => {
    let time = timeArr[$1] || '00';
    return time.length < 2 ? '0'+time : time
  })
} 
``` 
### 获取 URL 的查询参数

```js
// ?foo=bar&baz=bing => {foo: bar, baz: bing}
q = {};
location.search.replace(/([^?&=]+)=([^&]+)/g, (_, k, v) => (q[k] = v));

// [^?&=]  
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
 

<!--more-->  

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
 
 
### 数据类型校验

```js
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
```

 

