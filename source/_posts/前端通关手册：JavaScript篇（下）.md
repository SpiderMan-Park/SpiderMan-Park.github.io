---
title: 前端通关手册：JavaScript篇（下）
date: 2022-09-20 22:19:16
tags:
  - 前端
categories:
  - 前端
keywords:
  - JavaScript
top_img: /img/front/js_img/js.webp
cover: /img/front/js_img/js.webp
---

# EventEmitter

## 一、观察者模式

这就类似我们在微信平台订阅了公众号 , 当它有新的文章发表后，就会推送给我们所有订阅的人。

我们作为订阅者不必每次都去查看这个公众号有没有新文章发布，公众号作为发布者会在合适时间通知我们。

我们与公众号之间不再强耦合在一起。公众号不关心谁订阅了它， 不管你是男是女还是宠物狗，它只需要定时向所有订阅者发布消息即可。

### 观察者模式的优点

- 可以广泛应用于异步编程，它可以代替我们传统的回调函数
- 我们不需要关注对象在异步执行阶段的内部状态，我们只关心事件完成的时间点
- 取代对象之间硬编码通知机制，一个对象不必显式调用另一个对象的接口，而是松耦合的联系在一起 。

虽然不知道彼此的细节，但不影响相互通信。更重要的是，其中一个对象改变不会影响另一个对象。

## 二、Nodejs的EventEmitter

`Nodejs`的`EventEmitter`就是观察者模式的典型实现，`Nodejs`的`events`模块只提供了一个对象： `events.EventEmitter。EventEmitter` 的核心就是事件触发与事件监听器功能的封装。

> Node.js 里面的许多对象都会分发事件：一个 net.Server 对象会在每次有新连接时触发一个事件， 一个 fs.readStream 对象会在文件被打开的时候触发一个事件。 所有这些产生事件的对象都是 events.EventEmitter 的实例。

### Api

**addListener(event, listener)**

为指定事件添加一个监听器，默认添加到监听器数组的尾部。

**removeListener(event, listener)**

移除指定事件的某个监听器，监听器必须是该事件已经注册过的监听器。它接受两个参数，第一个是事件名称，第二个是回调函数名称。

**setMaxListeners(n)**

默认情况下， EventEmitters 如果你添加的监听器超过 10 个就会输出警告信息。 setMaxListeners 函数用于提高监听器的默认限制的数量。

**once(event, listener)**

为指定事件注册一个单次监听器，即 监听器最多只会触发一次，触发后立刻解除该监听器。

**emit(event, [arg1], [arg2], [...])**

按监听器的顺序执行执行每个监听器，如果事件有注册监听返回 `true`，否则返回 `false`。

### 基本使用

```javascript
var events = require('events');
var eventEmitter = new events.EventEmitter();

// 监听器 #1
var listener1 = function listener1() {
   console.log('监听器 listener1 执行。');
}

// 监听器 #2
var listener2 = function listener2() {
  console.log('监听器 listener2 执行。');
}

// 绑定 connection 事件，处理函数为 listener1 
eventEmitter.addListener('connection', listener1);

// 绑定 connection 事件，调用一次，处理函数为 listener2
eventEmitter.once('connection', listener2);

// 处理 connection 事件 
eventEmitter.emit('connection');

// 处理 connection 事件 
eventEmitter.emit('connection');
```



## 三、手动实现EventEmitter

```javascript
    function EventEmitter() {
      this._maxListeners = 10;
      this._events = Object.create(null);
    }

    // 向事件队列添加事件
    // prepend为true表示向事件队列头部添加事件
    EventEmitter.prototype.addListener = function (type, listener, prepend) {
      if (!this._events) {
        this._events = Object.create(null);
      }
      if (this._events[type]) {
        if (prepend) {
          this._events[type].unshift(listener);
        } else {
          this._events[type].push(listener);
        }
      } else {
        this._events[type] = [listener];
      }
    };

    // 移除某个事件
    EventEmitter.prototype.removeListener = function (type, listener) {
      if (Array.isArray(this._events[type])) {
        if (!listener) {
          delete this._events[type]
        } else {
          this._events[type] = this._events[type].filter(e => e !== listener && e.origin !== listener)
        }
      }
    };

    // 向事件队列添加事件，只执行一次
    EventEmitter.prototype.once = function (type, listener) {
      const only = (...args) => {
        listener.apply(this, args);
        this.removeListener(type, listener);
      }
      only.origin = listener;
      this.addListener(type, only);
    };

    // 执行某类事件
    EventEmitter.prototype.emit = function (type, ...args) {
      if (Array.isArray(this._events[type])) {
        this._events[type].forEach(fn => {
          fn.apply(this, args);
        });
      }
    };

    // 设置最大事件监听个数
    EventEmitter.prototype.setMaxListeners = function (count) {
      this.maxListeners = count;
    };
```



测试代码：

```javascript
    var emitter = new EventEmitter();

    var onceListener = function (args) {
      console.log('我只能被执行一次', args, this);
    }

    var listener = function (args) {
      console.log('我是一个listener', args, this);
    }

    emitter.once('click', onceListener);
    emitter.addListener('click', listener);

    emitter.emit('click', '参数');
    emitter.emit('click');

    emitter.removeListener('click', listener);
    emitter.emit('click');
```



## 四、JavaScript自定义事件

`DOM`也提供了类似上面`EventEmitter`的`API`，基本使用：

```javascript
//1、创建事件
var myEvent = new Event("myEvent");

//2、注册事件监听器
elem.addEventListener("myEvent",function(e){
  
})

//3、触发事件
elem.dispatchEvent(myEvent);
```

# 谈谈this对象的理解

![img](https://static.vue-js.com/46c820d0-74b7-11eb-85f6-6fac77c0c9b3.png)

## 一、定义

函数的 `this` 关键字在 `JavaScript` 中的表现略有不同，此外，在严格模式和非严格模式之间也会有一些差别

在绝大多数情况下，函数的调用方式决定了 `this` 的值（运行时绑定）

`this` 关键字是函数运行时自动生成的一个内部对象，只能在函数内部使用，总指向调用它的对象

举个例子：

```javascript
function baz() {
    // 当前调用栈是：baz
    // 因此，当前调用位置是全局作用域
    
    console.log( "baz" );
    bar(); // <-- bar的调用位置
}

function bar() {
    // 当前调用栈是：baz --> bar
    // 因此，当前调用位置在baz中
    
    console.log( "bar" );
    foo(); // <-- foo的调用位置
}

function foo() {
    // 当前调用栈是：baz --> bar --> foo
    // 因此，当前调用位置在bar中
    
    console.log( "foo" );
}

baz(); // <-- baz的调用位置
```

同时，`this`在函数执行过程中，`this`一旦被确定了，就不可以再更改

```javascript
var a = 10;
var obj = {
  a: 20
}

function fn() {
  this = obj; // 修改this，运行后会报错
  console.log(this.a);
}

fn();
```

## 二、绑定规则

根据不同的使用场合，`this`有不同的值，主要分为下面几种情况：

- 默认绑定
- 隐式绑定
- new绑定
- 显示绑定

### 默认绑定

全局环境中定义`person`函数，内部使用`this`关键字

```javascript
var name = 'Jenny';
function person() {
    return this.name;
}
console.log(person());  //Jenny
```

上述代码输出`Jenny`，原因是调用函数的对象在游览器中位`window`，因此`this`指向`window`，所以输出`Jenny`

注意：

严格模式下，不能将全局对象用于默认绑定，this会绑定到`undefined`，只有函数运行在非严格模式下，默认绑定才能绑定到全局对象

### 隐式绑定

函数还可以作为某个对象的方法调用，这时`this`就指这个上级对象

```javascript
function test() {
  console.log(this.x);
}

var obj = {};
obj.x = 1;
obj.m = test;

obj.m(); // 1
```

这个函数中包含多个对象，尽管这个函数是被最外层的对象所调用，`this`指向的也只是它上一级的对象

```javascript
var o = {
    a:10,
    b:{
        fn:function(){
            console.log(this.a); //undefined
        }
    }
}
o.b.fn();
```

上述代码中，`this`的上一级对象为`b`，`b`内部并没有`a`变量的定义，所以输出`undefined`

这里再举一种特殊情况

```javascript
var o = {
    a:10,
    b:{
        a:12,
        fn:function(){
            console.log(this.a); //undefined
            console.log(this); //window
        }
    }
}
var j = o.b.fn;
j();
```

此时`this`指向的是`window`，这里的大家需要记住，`this`永远指向的是最后调用它的对象，虽然`fn`是对象`b`的方法，但是`fn`赋值给`j`时候并没有执行，所以最终指向`window`

### new绑定

通过构建函数`new`关键字生成一个实例对象，此时`this`指向这个实例对象

```javascript
function test() {
　this.x = 1;
}

var obj = new test();
obj.x // 1
```

上述代码之所以能过输出1，是因为`new`关键字改变了`this`的指向

这里再列举一些特殊情况：

`new`过程遇到`return`一个对象，此时`this`指向为返回的对象

```javascript
function fn()  
{  
    this.user = 'xxx';  
    return {};  
}
var a = new fn();  
console.log(a.user); //undefined
```

如果返回一个简单类型的时候，则`this`指向实例对象

```javascript
function fn()  
{  
    this.user = 'xxx';  
    return 1;
}
var a = new fn;  
console.log(a.user); //xxx
```

注意的是`null`虽然也是对象，但是此时`new`仍然指向实例对象

```javascript
function fn()  
{  
    this.user = 'xxx';  
    return null;
}
var a = new fn;  
console.log(a.user); //xxx
```

### 显示修改

`apply()、call()、bind()`是函数的一个方法，作用是改变函数的调用对象。它的第一个参数就表示改变后的调用这个函数的对象。因此，这时`this`指的就是这第一个参数

```javascript
var x = 0;
function test() {
　console.log(this.x);
}

var obj = {};
obj.x = 1;
obj.m = test;
obj.m.apply(obj) // 1
```

## 三、箭头函数

在 ES6 的语法中还提供了箭头函语法，让我们在代码书写时就能确定 `this` 的指向（编译时绑定）

举个例子：

```javascript
const obj = {
  sayThis: () => {
    console.log(this);
  }
};

obj.sayThis(); // window 因为 JavaScript 没有块作用域，所以在定义 sayThis 的时候，里面的 this 就绑到 window 上去了
const globalSay = obj.sayThis;
globalSay(); // window 浏览器中的 global 对象
```

虽然箭头函数的`this`能够在编译的时候就确定了`this`的指向，但也需要注意一些潜在的坑

下面举个例子：

绑定事件监听

```javascript
const button = document.getElementById('mngb');
button.addEventListener('click', ()=> {
    console.log(this === window) // true
    this.innerHTML = 'clicked button'
})
```

上述可以看到，我们其实是想要`this`为点击的`button`，但此时`this`指向了`window`

包括在原型上添加方法时候，此时`this`指向`window`

```javascript
Cat.prototype.sayName = () => {
    console.log(this === window) //true
    return this.name
}
const cat = new Cat('mm');
cat.sayName()
```

同样的，箭头函数不能作为构建函数

## 四、优先级

### 隐式绑定 VS 显式绑定

```javascript
function foo() {
    console.log( this.a );
}

var obj1 = {
    a: 2,
    foo: foo
};

var obj2 = {
    a: 3,
    foo: foo
};

obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call( obj2 ); // 3
obj2.foo.call( obj1 ); // 2
```

显然，显示绑定的优先级更高

### new绑定 VS 隐式绑定

```javascript
function foo(something) {
    this.a = something;
}

var obj1 = {
    foo: foo
};

var obj2 = {};

obj1.foo( 2 );
console.log( obj1.a ); // 2

obj1.foo.call( obj2, 3 );
console.log( obj2.a ); // 3

var bar = new obj1.foo( 4 );
console.log( obj1.a ); // 2
console.log( bar.a ); // 4
```

可以看到，new绑定的优先级`>`隐式绑定

### `new`绑定 VS 显式绑定

因为`new`和`apply、call`无法一起使用，但硬绑定也是显式绑定的一种，可以替换测试

```javascript
function foo(something) {
    this.a = something;
}

var obj1 = {};

var bar = foo.bind( obj1 );
bar( 2 );
console.log( obj1.a ); // 2

var baz = new bar( 3 );
console.log( obj1.a ); // 2
console.log( baz.a ); // 3
```

```
bar`被绑定到obj1上，但是`new bar(3)` 并没有像我们预计的那样把`obj1.a`修改为3。但是，`new`修改了绑定调用`bar()`中的`this
```

我们可认为`new`绑定优先级`>`显式绑定

综上，new绑定优先级 > 显示绑定优先级 > 隐式绑定优先级 > 默认绑定优先级

# 防抖和节流

> 防抖和节流本质上都是优化高频率执行代码的一种手段

## 一、防抖

防抖（`debounce`）：不管事件触发频率多高，一定在事件触发`n`秒后才执行，如果你在一个事件触发的 `n` 秒内又触发了这个事件，就以新的事件的时间为准，`n`秒后才执行，总之，触发完事件 `n` 秒内不再触发事件，`n`秒后再执行。

![foo](https://www.conardli.top/%E8%8A%82%E6%B5%81%E9%98%B2%E6%8A%96.gif)

### 应用场景

#### 窗口大小变化，调整样式

```javascript
window.addEventListener('resize', debounce(handleResize, 200));
```

#### 搜索框，输入后1000毫秒搜索

```javascript
debounce(fetchSelectData, 300);
```

#### 表单验证，输入1000毫秒后验证

```javascript
debounce(validator, 1000);
```

### 实现

注意考虑两个问题：

- 在`debounce`函数中返回一个闭包，这里用的普通`function`，里面的`setTimeout`则用的箭头函数，这样做的意义是让`this`的指向准确，`this`的真实指向并非`debounce`的调用者，而是返回闭包的调用者。
- 对传入闭包的参数进行透传。

```javascript
    function debounce(event, time) {
      let timer = null;
      return function (...args) {
        clearTimeout(timer);
        timer = setTimeout(() => {
          event.apply(this, args);
        }, time);
      };
    }
```

有时候我们需要让函数立即执行一次，再等后面事件触发后等待`n`秒执行，我们给`debounce`函数一个`flag`用于标示是否立即执行。

当定时器变量`timer`为空时，说明是第一次执行，我们立即执行它。

```javascript
    function debounce(event, time, flag) {
      let timer = null;
      return function (...args) {
        clearTimeout(timer);
        if (flag && !timer) {
          event.apply(this, args);
        }
        timer = setTimeout(() => {
          event.apply(this, args);
        }, time);
      };
    }
```

## 二、节流

节流（`throttle`）:不管事件触发频率多高，只在单位时间内执行一次。

![foo](https://www.conardli.top/%E8%8A%82%E6%B5%81%E9%98%B2%E6%8A%96.gif)

### 实现

有两种方式可以实现节流，使用时间戳和定时器。

#### 时间戳实现

> 第一次事件肯定触发，最后一次不会触发

```javascript
    function throttle(event, time) {
      let pre = 0;
      return function (...args) {
        if (Date.now() - pre > time) {
          pre = Date.now();
          event.apply(this, args);
        }
      }
```

#### 定时器实现

> 第一次事件不会触发，最后一次一定触发

```javascript
    function throttle(event, time) {
      let timer = null;
      return function (...args) {
        if (!timer) {
          timer = setTimeout(() => {
            timer = null;
            event.apply(this, args);
          }, time);
        }
      }
    }
```

#### 结合版

> 定时器和时间戳的结合版，也相当于节流和防抖的结合版，第一次和最后一次都会触发

```javascript
    function throttle(event, time) {
      let pre = 0;
      let timer = null;
      return function (...args) {
        if (Date.now() - pre > time) {
          clearTimeout(timer);
          timer = null;
          pre = Date.now();
          event.apply(this, args);
        } else if (!timer) {
          timer = setTimeout(() => {
            event.apply(this, args);
          }, time);
        }
      }
    }
```

# 函数柯里化

## 一、定义

在数学和计算机科学中，柯里化是一种将使用多个参数的一个函数转换成一系列使用一个参数的函数的技术。

> 通俗易懂的解释：用闭包把参数保存起来，当参数的数量足够执行函数了，就开始执行函数。

## 二、实现

- 判断当前函数传入的参数是否大于或等于`fn`需要参数的数量，如果是，直接执行`fn`
- 如果传入参数数量不够，返回一个闭包，暂存传入的参数，并重新返回`currying`函数

```javascript
    function currying(fn, ...args) {
      if (args.length >= fn.length) {
        return fn(...args);
      } else {
        return (...args2) => currying(fn, ...args, ...args2);
      }
    }
```



我们来一个简单的实例验证一下：

```javascript
    const curryingFun = currying(fun)
    curryingFun(1)(2)(3);  // 1 2 3 
    curryingFun(1, 2)(3);  // 1 2 3 
    curryingFun(1, 2, 3);  // 1 2 3 
```



## 三、应用场景

### 参数复用

```javascript
    function getUrl(protocol, domain, path) {
      return protocol + "://" + domain + "/" + path;
    }

    var page1 = getUrl('http', 'www.zhangdeyu.vip', 'page1.html');
    var page2 = getUrl('http', 'www.zhangdeyu.vip', 'page2.html');
```



我们使用`currying`来简化它：

```javascript
let conardliSite = currying(simpleURL)('http', 'www.zhangdeyu.vip');
let page1 = conardliSite('page1.html');    
```

# 数组去重、扁平、最值

## 一、去重

### Object

开辟一个外部存储空间用于标示元素是否出现过。

```javascript
const unique = (array)=> {
    var container = {};
    return array.filter((item, index) =>  container.hasOwnProperty(item) ? false : (container[item] = true));
}
```

### indexOf + filter

```javascript
const unique = arr => arr.filter((e,i) => arr.indexOf(e) === i);
```

### Set

```javascript
const unique = arr => Array.from(new Set(arr));
```

```javascript
const unique = arr => [...new Set(arr)];
```

### 排序

通过比较相邻数字是否重复，将排序后的数组进行去重。

```javascript
    const unique = (array) => {
      array.sort((a, b) => a - b);
      let pre = 0;
      const result = [];
      for (let i = 0; i < array.length; i++) {
        if (!i || array[i] != array[pre]) {
          result.push(array[i]);
        }
        pre = i;
      }
      return result;
    }
```

### 去除重复的值

不同于上面的去重，这里是只要数字出现了重复次，就将其移除掉。

```javascript
const filterNonUnique = arr => arr.filter(i => 
  arr.indexOf(i) === arr.lastIndexOf(i)
)
```



## 二、扁平

### 基本实现

```javascript
    const flat = (array) => {
      let result = [];
      for (let i = 0; i < array.length; i++) {
        if (Array.isArray(array[i])) {
          result = result.concat(flat(array[i]));
        } else {
          result.push(array[i]);
        }
      }
      return result;
    }
```

### 使用reduce简化

```javascript
    function flatten(array) {
      return array.reduce(
        (target, current) =>
          Array.isArray(current) ?
            target.concat(flatten(current)) :
            target.concat(current)
        , [])
    }
```

### 根据指定深度扁平数组

```javascript
    function flattenByDeep(array, deep = 1) {
      return array.reduce(
        (target, current) =>
          Array.isArray(current) && deep > 1 ?
            target.concat(flattenByDeep(current, deep - 1)) :
            target.concat(current)
        , [])
    }
```

## 三、最值

### reduce

```javascript
array.reduce((c,n)=>Math.max(c,n))
```

### Math.max

`Math.max`参数原本是一组数字，只需要让他可以接收数组即可。

```javascript
const array = [3,2,1,4,5];
Math.max.apply(null,array);
Math.max(...array);
```

## 四、使用reduce实现map

```javascript
    Array.prototype.reduceToMap = function (handler) {
      return this.reduce((target, current, index) => {
        target.push(handler.call(this, current, index))
        return target;
      }, [])
    };
```

## 五、使用reduce实现filter

```javascript
    Array.prototype.reduceToFilter = function (handler) {
      return this.reduce((target, current, index) => {
        if (handler.call(this, current, index)) {
          target.push(current);
        }
        return target;
      }, [])
    };
```