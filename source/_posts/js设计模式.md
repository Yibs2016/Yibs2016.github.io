---
title: 设计模式
---

### 几个概念

> > 模块——从逻辑上将系统分解为更细微的部分，分而治之，复杂问题拆解为若干简单问题，逐个解决。
> > 耦合主要描述模块之间的关系， 内聚主要描述模块内部。
> > 耦合：模块之间存在依赖， 导致改动可能会互相影响， 关系越紧密， 耦合越强， 模块独立性越差。
> > 若模块 A 直接操作了模块 B 中数据， 则视为强耦合， 若 A 只是通过数据与模块 B 交互， 则视为弱耦合。
> > 内聚：模块内部的元素， 关联性越强， 则内聚越高， 模块单一性更强。
> > 设计原则
> > 单一职责原则：一个类只负责一个功能领域中的相应职责。
> > 开闭原则：一个软件实体应当对扩展开放，对修改关闭。
> > 依赖倒转原则: 抽象不应依赖于细节, 细节应当依赖于抽象. 即要针对接口编程, 而不是针对实现编程.

<!--more-->

### 寄生继承

```js
function Vehicle() {
  this.engines = 1;
}

Vehicle.prototype.ignition = function() {
  console.log("Turning on my engine.");
};

Vehicle.prototype.drive = function() {
  this.ignition();
  console.log("Steering and moving forward!");
};
//“寄生类”Car
function Car() {
  // 首先，car 是一个 Vehicle
  var car = new Vehicle();
  // 接着我们对 car 进行定制
  car.wheels = 4;
  // 保存到 Vehicle::drive() 的特殊引用
  var vehDrive = car.drive;
  // 重写 Vehicle::drive()
  car.drive = function() {
    vehDrive.call(this);
    console.log("Rolling on all " + this.wheels + " wheels!");
  };

  return car;
}

var myCar = new Car();

myCar.drive();
//首先复制一份 Vehicle 父类（对象）的定义，然后混入子类（对象）的定义（需要的话保留父类的特殊引用），然后用这个复合对象构建实例。
```

### 寄生组合式继承

```js
function creatBook(obj) {
  var o = new inheritObject(obj);
  o.getName = function() {}; //拓展新对象
  return o;
}
```

#### 状态模式

```js
var MarryState = function() {
  var _currentState = {},
    status = {
      jump: function() {
        console.log("jump");
      },
      move: function() {
        console.log("move");
      },
      shoot: function() {
        console.log("shoot");
      }
    };
  var A = {
    change: function() {
      var arg = arguments;

      _currentState = {};
      if (arg.length) {
        for (var i = 0, len = arg.length; i < len; i++) {
          _currentState[arg[i]] = true;
        }
      }

      return this;
    },
    go: function() {
      console.log("触发动作");
      for (var i in _currentState) {
        status[i] && status[i]();
      }

      return this;
    }
  };

  return {
    change: A.change,
    go: A.go
  };
};

var marry = new MarryState(); //marry为函数MarryState的返回值
marry
  .change("jump", "shoot")
  .go()
  .go()
  .change("shoot")
  .go();
//MarryState().change('jump','shoot').go().go().change('shoot').go();
//jump shoot jump shoot jump
```

### 策略模式之表单校验
<<<<<<< HEAD
=======

>>>>>>> 0245cebcdb8b4d6747d0e6040e2047aeaccc49af
```js
var strategies = {
  isNonEmpty: function(value, errorMsg) {
    // 不为空
    if (value === "") {
      return errorMsg;
    }
  },
  minLength: function(value, length, errorMsg) {
    // 限制最小长度
    if (value.length < length) {
      return errorMsg;
    }
  },
  isMobile: function(value, errorMsg) {
    // 手机号码格式
    if (!/(^1[3|5|8][0-9]{9}$)/.test(value)) {
      return errorMsg;
    }
  }
};
var Validator = function() {
  this.cache = []; // 保存校验规则
};

Validator.prototype.add = function(dom, rule, errorMsg) {
  var args = rule.split(":"); // 把strategy和参数分开
  this.cache.push(function() {
    // 把校验的步骤用空函数包装起来，并且放入cache
    var strategy = args.shift(); // 用户挑选的strategy
    args.unshift(dom.value); // 把input的value添加进参数列表
    args.push(errorMsg); // 把errorMsg添加进参数列表
    return strategies[strategy].apply(dom, args);
  });
};

Validator.prototype.start = function() {
  for (var i = 0, validatorFunc; (validatorFunc = this.cache[i++]); ) {
    var msg = validatorFunc(); // 开始校验，并取得校验后的返回信息
    if (msg) {
      // 如果有确切的返回值，说明校验没有通过
      return msg;
    }
  }
};

var validataFunc = function() {
  var validator = new Validator(); // 创建一个validator对象
  /***************添加一些校验规则****************/
  validator.add(registerForm.userName, "isNonEmpty", "用户名不能为空");
  validator.add(registerForm.password, "minLength:6", "密码长度不能少于6位");
  validator.add(registerForm.phoneNumber, "isMobile", "不是正确的手机号码");
  var errorMsg = validator.start(); // 获得校验结果
  return errorMsg; // 返回校验结果
};

var registerForm = document.getElementById("registerForm");

registerForm.onsubmit = function() {
  var errorMsg = validataFunc(); // 如果errorMsg有确切的返回值，说明未通过校验
  if (errorMsg) {
    alert(errorMsg);
    return false; // 阻止表单提交
  }
};

//某个文本框添加多种校验规则
Validator.prototype.add = function(dom, rules) {
  var self = this;

  for (var i = 0, rule; (rule = rules[i++]); ) {
    (function(rule) {
      var strategyAry = rule.strategy.split(":");
      var errorMsg = rule.errorMsg;

      self.cache.push(function() {
        var strategy = strategyAry.shift();

        strategyAry.unshift(dom.value);
        strategyAry.push(errorMsg);
        return strategies[strategy].apply(dom, strategyAry);
      });
    })(rule);
  }
};

Validator.prototype.start = function() {
  for (var i = 0, validatorFunc; (validatorFunc = this.cache[i++]); ) {
    var errorMsg = validatorFunc();

    if (errorMsg) {
      return errorMsg;
    }
  }
};

validator.add(registerForm.userName, [
  {
    strategy: "isNonEmpty",
    errorMsg: "用户名不能为空"
  },
  {
    strategy: "minLength:6",
    errorMsg: "用户名长度不能小于10位"
  }
]);
```

### 虚拟代理之图片预加载

```js
var myImage = (function() {
  var imgNode = document.createElement("img");

  document.body.appendChild(imgNode);
  return {
    setSrc: function(src) {
      imgNode.src = src;
    }
  };
})();
var proxyImage = (function() {
  var img = new Image();

  img.onload = function() {
    myImage.setSrc(this.src); //this.src即img.src
  };

  return {
    setSrc: function(src) {
      myImage.setSrc("file://C:/Users/loading.gif");
      img.src = src;
    }
  };
})();

proxyImage.setSrc("http:// imgcache.qq.com/yA0Nk.jpg");
//不用代理实现如下：
var MyImage = (function() {
  var imgNode = document.createElement("img");

  document.body.appendChild(imgNode);
  var img = new Image();

  img.onload = function() {
    imgNode.src = img.src;
  };

  return {
    setSrc: function(src) {
      imgNode.src = "file://C:/Users/loading.gif";
      img.src = src;
    }
  };
})();

MyImage.setSrc("http:// imgcache.qq.com/yA0Nk.jpg");

//代理的意义: 面向对象设计鼓励将行为分布到细粒度的对象中，如一个对象承担职责过多，等于把这些职责耦合到了一起，这种职责耦合导致脆弱和低内聚
//代理负责预加载图片，预加载的操作完成之后，把请求重新交给本体MyImage。
//此外，代理对象跟本体具有一致的"接口",即.setSrc(src)
```

### 缓存代理

> 为各种计算方法创建缓存代理

```js
/**************** 计算乘积 *****************/
var mult = function() {
  var a = 1;

  for (var i = 0, l = arguments.length; i < l; i++) {
    a = a * arguments[i];
  }

  return a;
};

/**************** 计算加和 *****************/
var plus = function() {
  var a = 0;

  for (var i = 0, l = arguments.length; i < l; i++) {
    a = a + arguments[i];
  }

  return a;
};
/**************** 创建缓存代理的工厂 *****************/
var createProxyFactory = function(fn) {
  var cache = {};

  return function() {
    var args = Array.prototype.join.call(arguments, ",");

    if (args in cache) {
      return cache[args];
    }

    return (cache[args] = fn.apply(this, arguments));
  };
};

var proxyMult = createProxyFactory(mult),
  proxyPlus = createProxyFactory(plus);

alert(proxyMult(1, 2, 3, 4)); // 输出：24
alert(proxyMult(1, 2, 3, 4)); // 输出：24
alert(proxyPlus(1, 2, 3, 4)); // 输出：10
alert(proxyPlus(1, 2, 3, 4)); // 输出：10
```

### 发布订阅

```js
//观察者
var Observer = (function() {
  var _msg = {};

  return {
    //注册信息
    regist: function(type, fn) {
      if (typeof _msg[type] === "undefined") {
        _msg[type] = [fn];
      } else {
        _msg[type].push(fn); //将动作方法推入该消息对应的动作执行队列中
      }
    },
    //发布信息
    fire: function(type, args) {
      if (!_msg[type]) return;
      var events = {
          //事件类型与参数
          type: type,
          args: args || {}
        },
        i = 0,
        len = _msg[type].length;

      for (; i < len; i++) {
        _msg[type][i].call(this, events);
      }
    },
    //移除信息
    remove: function() {
      if (typeof _msg[type] instanceof Array) {
        //存在该消息队列
        var i = _msg[type].length - 1;

        for (; i >= 0; i--) {
          _msg[type][i] === fn && _msg[type].splce(i, 1); //移除相应动作
        }
      }
    }
  };
})();

Observer.regist("test", function(e) {
  console.log(e.type, e.args.msg);
});
Observer.fire("test", {
  msg: "这是个参数"
});

//给所有的对象都动态安装发布—订阅功能
<<<<<<< HEAD
var installEvent = function( obj ) {
    for ( var i in Observer ){
        obj[ i ] = Observer[ i ];
    }
=======
var installEvent = function(obj) {
  for (var i in Observer) {
    obj[i] = Observer[i];
  }
>>>>>>> 0245cebcdb8b4d6747d0e6040e2047aeaccc49af
};

var salesOffices = {};

installEvent(salesOffices);
```
