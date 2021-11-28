## 1.请详细描述一下 js 的 new 操作

一句话介绍`new`

`new`运算符创建一个用户定义的对象类型的实例或者具有构造函数的内置对象的实例

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}
Person.prototype.sayName = function () {
  console.log(this.name);
};
let fyj = new Person("fyj", 21);
console.log(fyj); // Person {name:'fyj', age:21}
fyj.sayName(); // 'fyj'
```

`new`通过 Person 创建出来的实例可以访问到构造函数的属性

`new`通过 Person 创建出来的实例可以访问到构造函数原型链上的方法和属性

在构造函数内加上返回值是什么呢？
**1、返回基本类型**

```js
function Car(price) {
  this.price = price;
  return 20;
}
let bigCar = new Car(90);
console.log(bigCar.price); // 90
```

返回值是原始值的时候，那么这返回值没有任何意义

**2、返回引用数据类型**

```js
function Car(price) {
  this.price = price;
  return { km: 200 };
}
let bigCar = new Car(90);
console.log(bigCar.price, bigCar.km); //undefined ,200
```

返回值是引用数据类型的时候，那么这个返回值就会被正常使用

**new 的实现**

- 新建一个对象`obj`
- 把`obj`内部的`prototype`特性被赋值为构造函数的`prototype`属性
- 构造函数内部的`this`被赋值为这个新对象（即`this`指向新对象）
- 执行构造函数内部的代码（即给新对象添加属性）
- 如果构造函数返回非空对象，则返回该对象，否则返回刚创建的新对象

```js
function myNew () {
  let obj = Object.create(null) // 创建一个新的对象
  //取出参数的第一个参数 获得构造函数
  Constructor = [].shift.call(arguments)
  // 连接原型 让实例可以访问原型链中的属性
  obj.__proto__ = Constructor.prototype
  // 执行构造函数 绑定this 为这个新对象添加属性
  let ret = Constructor.apply(obj,arguments) //apply得细看
  return type ret === 'object' ? ret : obj
}
```

## 2.this 指向问题

`this` 参数是面向对象 js 编程的一个重要组成部分，代表函数调用的相关联的对象，也称为函数上下文。

`this`指向大致分为三种调用模式

- 全局模式
- 方法模式
- 函数模式

其中函数模式又分为普通函数调用、带 call/apply/bind 的函数调用、构造函数调用、箭头函数调用
`this`指向的顺序口诀：箭头函数、new、bind、apply 和 call、欧比届点（obj.）、直接调用、不在函数里。

**全局模式**

```js
cosnole.log(this === window); // true

a = 21;
console.log(window.a); //21

this.b = "fyj";
console.log(window.b); //21
console.log(b); //21
```

**普通函数调用**
当普通的函数，直接调用的时候，一般来说分两种情况:

严格模式绑定到 undefined
非严格模式绑定到全局对象 window

```js
function foo() {
  console.log(this);
}
function bar() {
  "use strict";
  console.log(this);
}
foo(); // window
bar(); // undefined
```

**call/apply/bind 函数调用**
call/apply 这两个函数对象到方法能立即执行某个函数，并且将函数中的 this 绑定到我们提供到对象上去
bind 方法永久的绑定函数中的 this 到指定对象上，并返回一个新函数，将来这个函数无论怎么调用都可以

```js
function foo() {
  console.log(this);
}
function bar() {
  console.log(this);
}
foo.call({ name: "小米" }); // {name: "小米"}

const bar1 = bar.bind({ num: 123 });
bar1(); // {num: 123}  多次bind只认第一次bind 的值
```

**对象属性方法调用**
作为对象属性方法调用，都指向前面调用函数都那个对象。当然有的时候会出现各种变种或者干扰的面试题

```js
const student = {
  name: "tom",

  fn: function () {
    return this;
  },
};
console.log(student.fn() === student);
```

**构造函数调用或者类上下文**
构造函数作为 JavaScript 创建对象的那只大母鸡（实际上类是构造函数的语法糖），通常程序界有个段子叫做 new 一个对象，谁还敢说程序员（媛）没有对象的,这种方式调用 this 指向的是你 new 出来的那个对象实例本身:

```js
function Person(name) {
  console.log(this);
  this.name = name;
}

const p = new Person("tom");
console.log(p);
```

**箭头函数中的 this**
箭头函数体内的 this 对象，就是定义时所在的对象，而不是使用时所在的对象。

```js
var obj = {
  name: "tom",
  foo() {
    setTimeout(() => {
      console.log(this);
    }, 1000);
  },
};

obj.foo(); // obj
```

- `js`函数中如何绑定`this`到新对象上
  > 可以采用`call` `apply` `bind`来进行绑定
- `bind`和`call`有什么区别

  > ` call``apply``bind `三者都是来改变 this 指向的 call 第一个参数是 this 第二个参数是一个一个的 apply 的参数是一个数组
  > `bind`没有要求 可以是一个一个的 也可以是一个数组 call 和 apply 函数是直接执行的 而 bind 函数会返回一个新的函数 什么时候想调用的时候才会执行

- 手写`call`

```js
//call 改变了this的指向 指向到某对象
// 函数执行了

// 所以我们模拟的步骤可以分为：
// 将函数设为对象的属性
// 执行该函数
// 删除该函数
// 第三版
Function.prototype.call2 = function (context) {
  var context = context || window; // this可以为null 这个时候指向window
  context.fn = this;

  var args = []; //参数
  for (var i = 1, len = arguments.length; i < len; i++) {
    args.push("arguments[" + i + "]");
  }

  var result = eval("context.fn(" + args + ")");

  delete context.fn;
  return result;
};

// 测试一下
var value = 2;

var obj = {
  value: 1,
};

function bar(name, age) {
  console.log(this.value);
  return {
    value: this.value,
    name: name,
    age: age,
  };
}

bar.call(null); // 2

console.log(bar.call2(obj, "kevin", 18));
// 1
// Object {
//    value: 1,
//    name: 'kevin',
//    age: 18
// }
```

- 手写 apply

```js
Function.prototype.apply = function (context, arr) {
  var context = Object(context) || window;
  context.fn = this;

  var result;
  if (!arr) {
    result = context.fn();
  } else {
    var args = [];
    for (var i = 0, len = arr.length; i < len; i++) {
      args.push("arr[" + i + "]");
    }
    result = eval("context.fn(" + args + ")");
  }

  delete context.fn;
  return result;
};
```

- 手写 bind

```js
Function.prototype.bind2 = function (context) {
  if (typeof this !== "function") {
    throw new Error(
      "Function.prototype.bind - what is trying to be bound is not callable"
    );
  }

  var self = this;
  var args = Array.prototype.slice.call(arguments, 1);
  var fNOP = function () {};

  var fbound = function () {
    self.apply(
      this instanceof self ? this : context,
      args.concat(Array.prototype.slice.call(arguments))
    );
  };

  fNOP.prototype = this.prototype;
  fbound.prototype = new fNOP();

  return fbound;
};
```

## 3.什么是 js 闭包

## 4.setTimeout 为何能在单线程的 js 中执行

## 5.详细描述 es5 和 es6 中的作用域

> 作用域是程序源代码中定义变量的区域。

> 作用域规定了如何查找变量，也就是确定当前执行代码对变量的访问权限。
> `js`采用词法作用域，函数的作用域在函数定义的时候就已经决定了。 而动态作用域是在函数调用的时候才决定

## 6.详细描述异步 EventLoop 机制

首先，我们都知道`js`是单线程模式  `js`的单线程和它的用途是有关系的，`js`的主要用途是与用户互动，以及操作`DOM`，这决定了它只能是单线程  比如，假设`js`同时有两个线程，一个线程在某个`dom`节点上添加内容，另一个线程删除了这个节点，那么浏览器应该以谁为准呢？
`js`虽然还有`Worker`线程  没错，为了利用多核`CPU`的计算能力，`h5`提出了`web worker` 允许js脚本创建多个线程，但是子线程完全受主线程控制，并且不得操作`DOM`

浏览器是多进程

- Browser进程
> 浏览器的主进程（负责协调、主控制），该线程只有一个
> 负责浏览器的界面展示 与用户交互 如前进、后退等
> 负责各个页面的管理，创建和销毁其他进程
> 将渲染进程得到的内存的Bitmap(位图) 绘制到用户界面上
> 网络资源的管理 下载等

- 第三方插件进程
> 每种类型的插件对应一个进程，当使用该插件时才创建

- GPU进程
> 该进程也只有一个，用于3D绘制等等

- 渲染进程(重)

> 即通常所说的浏览器内核(Renderer进程，内部是多线程),每个Tab页面都有一个渲染进程，互不影响
> 主要作用为页面渲染，脚本执行，事件处理等

为什么浏览器要多进程
我们假设浏览器是单进程，那么某个Tab页崩溃了，就影响了整个浏览器，体验有多差
同理如果插件崩溃了也会影响整个浏览器
浏览器进程有很多，每个进程又有很多线程，都会占用内存,这也意味着内存等资源消耗会很大，有点拿空间换时间的意思
到此可不只是为了让我们理解为何Chrome运行时间长了电脑会卡.

- 简述渲染进程Renderer(重)
页面的渲染，JS的执行，事件的循环，都在渲染进程内执行，所以我们要重点了解渲染进程
渲染进程是多线程的，我们来看渲染进程的一些常用较为主要的线程


渲染进程Renderer的主要线程
- GUI渲染线程

负责渲染浏览器界面，解析HTML，CSS，构建DOM树和RenderObject树，布局和绘制等

解析html代码(HTML代码本质是字符串)转化为浏览器认识的节点，生成DOM树，也就是DOM Tree
解析css，生成CSSOM(CSS规则树)
把DOM Tree 和CSSOM结合，生成Rendering Tree(渲染树)

当我们修改了一些元素的颜色或者背景色，页面就会重绘(Repaint)
当我们修改元素的尺寸，页面就会回流(Reflow)
当页面需要Repaing和Reflow时GUI线程执行，绘制页面
回流(Reflow)比重绘(Repaint)的成本要高，我们要尽量避免Reflow和Repaint
GUI渲染线程与JS引擎线程是互斥的

当JS引擎执行时GUI线程会被挂起(相当于被冻结了)
GUI更新会被保存在一个队列中等到JS引擎空闲时立即被执行

- JS引擎线程

JS引擎线程就是JS内核，负责处理Javascript脚本程序(例如V8引擎)
JS引擎线程负责解析Javascript脚本，运行代码
JS引擎一直等待着任务队列中任务的到来，然后加以处理

浏览器同时只能有一个JS引擎线程在运行JS程序，所以js是单线程运行的
一个Tab页(renderer进程)中无论什么时候都只有一个JS线程在运行JS程序


GUI渲染线程与JS引擎线程是互斥的，js引擎线程会阻塞GUI渲染线程

就是我们常遇到的JS执行时间过长，造成页面的渲染不连贯，导致页面渲染加载阻塞(就是加载慢)
例如浏览器渲染的时候遇到<script>标签，就会停止GUI的渲染，然后js引擎线程开始工作，执行里面的js代码，等js执行完毕，js引擎线程停止工作，GUI继续渲染下面的内容。所以如果js执行时间太长就会造成页面卡顿的情况

- 事件触发线程

属于浏览器而不是JS引擎，用来控制事件循环，并且管理着一个事件队列(task queue)
当js执行碰到事件绑定和一些异步操作(如setTimeOut，也可来自浏览器内核的其他线程，如鼠标点击、AJAX异步请求等)，会走事件触发线程将对应的事件添加到对应的线程中(比如定时器操作，便把定时器事件添加到定时器线程)，等异步事件有了结果，便把他们的回调操作添加到事件队列，等待js引擎线程空闲时来处理。
当对应的事件符合触发条件被触发时，该线程会把事件添加到待处理队列的队尾，等待JS引擎的处理
因为JS是单线程，所以这些待处理队列中的事件都得排队等待JS引擎处理

- 定时触发器线程

setInterval与setTimeout所在线程
浏览器定时计数器并不是由JavaScript引擎计数的(因为JavaScript引擎是单线程的，如果处于阻塞线程状态就会影响记计时的准确)
通过单独线程来计时并触发定时(计时完毕后，添加到事件触发线程的事件队列中，等待JS引擎空闲后执行)，这个线程就是定时触发器线程，也叫定时器线程
W3C在HTML标准中规定，规定要求setTimeout中低于4ms的时间间隔算为4ms

- 异步http请求线程

在XMLHttpRequest在连接后是通过浏览器新开一个线程请求
将检测到状态变更时，如果设置有回调函数，异步线程就产生状态变更事件，将这个回调再放入事件队列中再由JavaScript引擎执行
简单说就是当执行到一个http异步请求时，就把异步请求事件添加到异步请求线程，等收到响应(准确来说应该是http状态变化)，再把回调函数添加到事件队列，等待js引擎线程来执行


**eventloop**
同步任务都在主线程(这里的主线程就是JS引擎线程)上执行，会形成一个执行栈
主线程之外，事件触发线程管理着一个任务队列，只要异步任务有了运行结果，就在任务队列之中放一个事件回调
一旦执行栈中的所有同步任务执行完毕(也就是JS引擎线程空闲了)，系统就会读取任务队列，将可运行的异步任务(任务队列中的事件回调，只要任务队列中有事件回调，就说明可以执行)添加到执行栈中，开始执行

我们可以将每次执行栈执行的代码当做是一个宏任务(包括每次从事件队列中获取一个事件回调并放到执行栈中执行)， 每一个宏任务会从头到尾执行完毕，不会执行其他
由于JS引擎线程和GUI渲染线程是互斥的关系，浏览器为了能够使宏任务和DOM任务有序的进行，会在一个宏任务执行结果后，在下一个宏任务执行前，GUI渲染线程开始工作，对页面进行渲染
我们已经知道宏任务结束后，会执行渲染，然后执行下一个宏任务， 而微任务可以理解成在当前宏任务执行后立即执行的任务

当一个宏任务执行完，会在渲染前，将执行期间所产生的所有微任务都执行完
```shell
## 宏任务-->微任务--> GUI渲染 --> 宏任务....
```

## 7.Promise.all 的用法
`promise` 构造函数是同步  .then 是异步 
- 如何让 Promise.all 在抛出异常后依然有效
- Promise 有几种状态
- 如何捕获 promise 错误
- promise 可以串联么
- promise 如何满足多个异步进程的同步顺序
- promise.race 和 promise.all 的区别
- async await

> await之前的代码相当于 promise中的构造函数的代码  await之后的代码相当于是Promsie的then的代码
- 如何顺序执行 10 个异步任务
- promiseA+规范

## 8.详述节流和防抖

- 手写节流和防抖

## 9.简述 es6 的新特性

## 10.简述箭头函数的特性

## 11.为什么需要深拷贝

- 什么是浅拷贝

## 12.简述指针是什么

## 13.如何监控未处理的异常

## 14.纯 js 如何获取 scrolltop 的值

## 15.arguments 如何转数组

## 16.移动端和 pc 端 click 事件为什么差了 300ms

## 17. == 和 ===区别

## 18. 原型和原型链是什么

- **构造函数创建对象**

```js
function Person(name) {
  // 实例成员
  this.name = name;
  this.sing = function () {
    console.log("我爱唱歌");
  };
}
Person.sex = "man";
let person = new Person("name");
let person1 = new Person();
let person2 = new Person();
console.log(person.name); //name
console.log(person.sex); // undefined  实例无法访问sex属性

console.log(Person.name); //Person 通过构造函数无法访问实例成员
console.log(Person.sex); // man  通过构造函数可以直接访问静态成员

person1.sing(); //我爱唱歌
person2.sing(); //我爱唱歌
console.log(person1.sing === person2.sing); //false
```

很明显，person1 和 person2 指向的不是一个地方。 所以 在构造函数上通过 this 来添加方法的方式来生成实例，每次生成实例，都是新开辟一个内存空间存方法。这样会导致内存的极大浪费，从而影响性能。
`Person`是一个构造函数，通过`new`创建了一个实例对象`person`
构造函数分为实例成员和静态成员
**实例成员：** 实例成员就是在构造函数内部，通过`this`添加的成员，实例成员只能通过实例化的对象来访问。
**静态成员：** 在构造函数本身上添加的成员，只能通过构造函数来访问

```js
function Person() {}
Person.prototype.name = "name";
let person1 = new Person();
let person2 = new Person();
console.log(person1.name); // name
console.log(person2.name); // name
```

函数的`prototype`属性指向了一个对象，这个对象正是调用该构造函数而创建的实例的原型。

每一个`JavaScript`对象在创建的时候就会与之关联另一个对象，这个对象就是我们所说的原型，每一个对象都会从原型“继承”属性。

通过原型分配的函数，是所有对象共享的 原型的作用就是共享方法

```js
function Star(name) {
  this.name = name;
}
Star.prototype.sing = function () {
  console.log("我爱唱歌", this.name);
};
let stu1 = new Star("小红");
let stu2 = new Star("小蓝");
stu1.sing(); //我爱唱歌 小红
stu2.sing(); //我爱唱歌 小蓝
console.log(stu1.sing === stu2.sing); //true
```

- proto
  每一个`JavaScript`对象都具有一个属性，叫`__proto__`，这个属性会指向该对象的原型

```js
console.log(person.__proto__ === Person.prototype); //true
```

**constructor**
原型指向构造函数是有的:`constructor`,每个原型都有一个`constructor`属性指向相关联的构造函数

```js
console.log(Person === Person.prototype.constructor); // true
```

当读取实例的属性的时候，如果找不到，就去查找与对象关联的原型种的属性，如果还查找不到，就去原型的原型中去查找
![原型链](/img/原型链.png)

## 19.三句话概括所有值传递类型、引用传递类型 以及如何用引用的方式传递值类型

## 20.js 基础类型

- null undefined 区别
- 怎么比较

## 21.指针和引用的区别

## 22.js 中对于不同环境的变量什么时候释放

- 在非闭包的情况下变量什么时候会被回收

## 23.js 继承说一下

- 原型链继承

```js
function Person() {
  this.name = "kevin";
}
Person.prototype.getName = function () {
  cosole.log(this.name);
};
function Child() {

}
Child.prototype = new Person()
let child1 = new Child()
console.log(child1.getName()) // kevin

// 引用类型的属性被所有实例共享，举个例子：
function Parent () {
    this.names = ['kevin', 'daisy'];
}

function Child () {

}

Child.prototype = new Parent();

var child1 = new Child();

child1.names.push('yayu');

console.log(child1.names); // ["kevin", "daisy", "yayu"]

var child2 = new Child();

console.log(child2.names); // ["kevin", "daisy", "yayu"]
// 在创建 Child 的实例时，不能向Parent传参
```

- es6类继承
```js
class Father {
        constructor(name){
            this.name = name;
        }
        dance(){
            return '我在跳舞';
        }
    }
    class Son extends Father{
        constructor(name,score){
            super(name);
            this.score = score;
        }
        sing(){
            return this.name +','+this.dance();
        }
    }
    let obj = new Son('小红',100);
```

- 借用构造函数（经典继承）
```js
function Parent () {
    this.names = ['kevin', 'daisy'];
}

function Child () {
    Parent.call(this);
}

var child1 = new Child();

child1.names.push('yayu');

console.log(child1.names); // ["kevin", "daisy", "yayu"]

var child2 = new Child();

console.log(child2.names); // ["kevin", "daisy"]
// 优点：

// 1.避免了引用类型的属性被所有实例共享

// 2.可以在 Child 中向 Parent 传参
// 缺点：

// 方法都在构造函数中定义，每次创建实例都会创建一遍方法。
```

- 组合继承
```js
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {

    Parent.call(this, name);

    this.age = age;

}

Child.prototype = new Parent();

var child1 = new Child('kevin', '18');

child1.colors.push('black');

console.log(child1.name); // kevin
console.log(child1.age); // 18
console.log(child1.colors); // ["red", "blue", "green", "black"]

var child2 = new Child('daisy', '20');

console.log(child2.name); // daisy
console.log(child2.age); // 20
console.log(child2.colors); // ["red", "blue", "green"]
// 融合原型链继承和构造函数的优点，是 JavaScript 中最常用的继承模式。
```
- 原型式继承
```js
function createObj(o) {
    function F(){}
    F.prototype = o;
    return new F();
}
// 就是 ES5 Object.create 的模拟实现，将传入的对象作为创建的对象的原型。
// 缺点：
// 包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样。
var person = {
    name: 'kevin',
    friends: ['daisy', 'kelly']
}

var person1 = createObj(person);
var person2 = createObj(person);

person1.name = 'person1';
console.log(person2.name); // kevin

person1.firends.push('taylor');
console.log(person2.friends); // ["daisy", "kelly", "taylor"]
// 注意：修改person1.name的值，person2.name的值并未发生改变，并不是因为person1和person2有独立的 name 值，而是因为person1.name = 'person1'，给person1添加了 name 值，并非修改了原型上的 name 值。
```
- 寄生式继承
```js
// 创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。
function createObj (o) {
    var clone = object.create(o);
    clone.sayName = function () {
        console.log('hi');
    }
    return clone;
}
// 缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法。
```
-  寄生组合式继承
```js
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}

Child.prototype = new Parent();

var child1 = new Child('kevin', '18');

console.log(child1)
// 组合继承最大的缺点是会调用两次父构造函数。
// 一次是设置子类型实例的原型的时候：
Child.prototype = new Parent();
// 一次在创建子类型实例的时候：
var child1 = new Child('kevin', '18');
// 回想下 new 的模拟实现，其实在这句中，我们会执行：
Parent.call(this, name);
// 在这里，我们又会调用了一次 Parent 构造函数。
// 所以，在这个例子中，如果我们打印 child1 对象，我们会发现 Child.prototype 和 child1 都有一个属性为colors，属性值为['red', 'blue', 'green']。
// 那么我们该如何精益求精，避免这一次重复调用呢？
// 如果我们不使用 Child.prototype = new Parent() ，而是间接的让 Child.prototype 访问到 Parent.prototype 呢？
// 看看如何实现：
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}

// 关键的三步
var F = function () {};

F.prototype = Parent.prototype;

Child.prototype = new F();


var child1 = new Child('kevin', '18');

console.log(child1);
// 最后我们封装一下这个继承方法：
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

function prototype(child, parent) {
    var prototype = object(parent.prototype);
    prototype.constructor = child;
    child.prototype = prototype;
}

// 当我们使用的时候：
prototype(Child, Parent);
// 引用《JavaScript高级程序设计》中对寄生组合式继承的夸赞就是：
// 这种方式的高效率体现它只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。
```
## 24.数组有哪些方法

## 25.事件委托原理

## 26.如何间隔 1s 输出数组元素

## 27.如何实现事件监听

- 有什么区别

## 28.js 创建对象的各种方式

- 工厂模式

```js
function createPerson(name) {
  var o = new Object();
  o.name = name;
  o.getName = function () {
    console.log(this.name);
  };

  return o;
}

var person1 = createPerson("kevin");
// 缺点：对象无法识别，因为所有的实例都指向一个原型
```

- 构造函数模式

```js
function Person(name) {
  this.name = name;
  this.getName = function () {
    console.log(this.name);
  };
}

var person1 = new Person("kevin");
// 优点：实例可以识别为一个特定的类型

// 缺点：每次创建实例时，每个方法都要被创建一次
```

- 构造函数模式优化

```js
function Person(name) {
  this.name = name;
  this.getName = getName;
}

function getName() {
  console.log(this.name);
}

var person1 = new Person("kevin");
// 优点：解决了每个方法都要被重新创建的问题

// 缺点：这叫啥封装……
```

- 原型模式

```js
function Person(name) {}

Person.prototype.name = "keivn";
Person.prototype.getName = function () {
  console.log(this.name);
};

var person1 = new Person();
// 优点：方法不会重新创建

// 缺点：1. 所有的属性和方法都共享 2. 不能初始化参数
```

- 原型模式优化

```js
function Person(name) {}

Person.prototype = {
  name: "kevin",
  getName: function () {
    console.log(this.name);
  },
};

var person1 = new Person();
// 优点：封装性好了一点
// 缺点：重写了原型，丢失了constructor属性
```

- 原型模式优化

```js
function Person(name) {}

Person.prototype = {
  constructor: Person,
  name: "kevin",
  getName: function () {
    console.log(this.name);
  },
};

var person1 = new Person();
// 优点：实例可以通过constructor属性找到所属构造函数
// 缺点：原型模式该有的缺点还是有
```

- 组合模式

```js
// 构造函数模式与原型模式双剑合璧。
function Person(name) {
  this.name = name;
}

Person.prototype = {
  constructor: Person,
  getName: function () {
    console.log(this.name);
  },
};

var person1 = new Person();
// 优点：该共享的共享，该私有的私有，使用最广泛的方式
// 缺点：有的人就是希望全部都写在一起，即更好的封装性
```

- 寄生构造函数模式

```js
function Person(name) {
  var o = new Object();
  o.name = name;
  o.getName = function () {
    console.log(this.name);
  };
  return o;
}

var person1 = new Person("kevin");
console.log(person1 instanceof Person); // false
console.log(person1 instanceof Object); // true
// 寄生构造函数模式，我个人认为应该这样读：
// 寄生-构造函数-模式，也就是说寄生在构造函数的一种方法。
// 也就是说打着构造函数的幌子挂羊头卖狗肉，你看创建的实例使用 instanceof 都无法指向构造函数！
// 这样方法可以在特殊情况下使用。比如我们想创建一个具有额外方法的特殊数组，但是又不想直接修改Array构造函数，我们可以这样写：
function SpecialArray() {
  var values = new Array();

  for (var i = 0, len = arguments.length; i < len; i++) {
    values.push(arguments[i]);
  }

  values.toPipedString = function () {
    return this.join("|");
  };
  return values;
}

var colors = new SpecialArray("red", "blue", "green");
var colors2 = SpecialArray("red2", "blue2", "green2");

console.log(colors);
console.log(colors.toPipedString()); // red|blue|green

console.log(colors2);
console.log(colors2.toPipedString()); // red2|blue2|green2复制代码你会发现，其实所谓的寄生构造函数模式就是比工厂模式在创建对象的时候，多使用了一个new，实际上两者的结果是一样的。
// 但是作者可能是希望能像使用普通 Array 一样使用 SpecialArray，虽然把 SpecialArray 当成函数也一样能用，但是这并不是作者的本意，也变得不优雅。
// 在可以使用其他模式的情况下，不要使用这种模式。
// 但是值得一提的是，上面例子中的循环：
for (var i = 0, len = arguments.length; i < len; i++) {
  values.push(arguments[i]);
}
// 可以替换成：
values.push.apply(values, arguments);
```

- 稳妥构造函数模式

```js
function person(name) {
  var o = new Object();
  o.sayName = function () {
    console.log(name);
  };
  return o;
}

var person1 = person("kevin");

person1.sayName(); // kevin

person1.name = "daisy";

person1.sayName(); // kevin

console.log(person1.name); // daisy
// 所谓稳妥对象，指的是没有公共属性，而且其方法也不引用 this 的对象。
// 与寄生构造函数模式有两点不同：

// 新创建的实例方法不引用 this
// 不使用 new 操作符调用构造函数

// 稳妥对象最适合在一些安全的环境中。
// 稳妥构造函数模式也跟工厂模式一样，无法识别对象所属类型。
```
