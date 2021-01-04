---
title: 行为委托
---

#### class

```js
class Widget {
  constructor(width, height) {
    this.width = width || 50;
    this.height = height || 50;
    this.$elem = null;
  }
  render($where) {
    if (this.$elem) {
      this.$elem
        .css({
          width: this.width + "px",
          height: this.height + "px"
        })
        .appendTo($where);
    }
  }
}
class Button extends Widget {
  constructor(width, height, label) {
    super(width, height);
    this.label = label || "Default";
    this.$elem = $("<button>").text(this.label);
  }
  render($where) {
    super($where);
    this.$elem.click(this.onClick.bind(this));
  }
  onClick(evt) {
    console.log("Button '" + this.label + "' clicked!");
  }
}
//class 字面语法不能声明属性(只能声明方法)  ->  避免原型链末端的“实例”意外获取其他地方的属性
//通过extends很自然地扩展对象(子)类型
```

<!--more-->

#### 对象关联

```js
var Widget = {
  init: function(width, height) {
    this.width = width || 50;
    this.height = height || 50;
    this.$elem = null;
  },
  insert: function($where) {
    if (this.$elem) {
      this.$elem
        .css({
          width: this.width + "px",
          height: this.height + "px"
        })
        .appendTo($where);
    }
  }
};
var Button = Object.create(Widget);
Button.setup = function(width, height, label) {
  // 委托调用
  this.init(width, height);
  this.label = label || "Default";
  this.$elem = $("<button>").text(this.label);
};
Button.build = function($where) {
  // 委托调用
  this.insert($where);
  this.$elem.click(this.onClick.bind(this));
};
Button.onClick = function(evt) {
  console.log("Button '" + this.label + "' clicked!");
};
$(document).ready(function() {
  var $body = $(document.body);
  var btn1 = Object.create(Button);
  btn1.setup(125, 30, "Hello");
  btn1.build($body);
});
```
