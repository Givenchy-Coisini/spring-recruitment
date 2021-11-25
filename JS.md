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
function foo(){
  console.log(this);  
}
function bar(){
  "use strict"; 
  console.log(this);
}
foo() // window
bar() // undefined
```

**call/apply/bind函数调用**
call/apply 这两个函数对象到方法能立即执行某个函数，并且将函数中的this绑定到我们提供到对象上去
bind 方法永久的绑定函数中的this到指定对象上，并返回一个新函数，将来这个函数无论怎么调用都可以
```js
function foo(){
  console.log(this);  
}
function bar(){
  console.log(this);
}
foo.call({name:'小米'}); // {name: "小米"}

const bar1 = bar.bind({num:123})
bar1() // {num: 123}  多次bind只认第一次bind 的值
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
构造函数作为JavaScript创建对象的那只大母鸡（实际上类是构造函数的语法糖），通常程序界有个段子叫做new 一个对象，谁还敢说程序员（媛）没有对象的,这种方式调用this指向的是你new出来的那个对象实例本身:
```js
function Person(name){
  console.log(this);
  this.name = name
}

const p = new Person('tom')
console.log(p);
```

**箭头函数中的this**
箭头函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
```js
var obj = {
  name: "tom",
  foo() {
    setTimeout(() => {
      console.log(this);
    }, 1000);
  },
};

obj.foo() // obj
```
- `js`函数中如何绑定`this`到新对象上
> 可以采用`call` `apply` `bind`来进行绑定
- `bind`和`call`有什么区别
> `call``apply``bind`三者都是来改变this指向的  call第一个参数是this 第二个参数是一个一个的  apply的参数是一个数组
`bind`没有要求 可以是一个一个的 也可以是一个数组  call和apply函数是直接执行的 而bind函数会返回一个新的函数 什么时候想调用的时候才会执行

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
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }

    var result = eval('context.fn(' + args +')');

    delete context.fn
    return result;
}

// 测试一下
var value = 2;

var obj = {
    value: 1
}

function bar(name, age) {
    console.log(this.value);
    return {
        value: this.value,
        name: name,
        age: age
    }
}

bar.call(null); // 2

console.log(bar.call2(obj, 'kevin', 18));
// 1
// Object {
//    value: 1,
//    name: 'kevin',
//    age: 18
// }
```
- 手写apply
```js
Function.prototype.apply = function (context, arr) {
    var context = Object(context) || window;
    context.fn = this;

    var result;
    if (!arr) {
        result = context.fn();
    }
    else {
        var args = [];
        for (var i = 0, len = arr.length; i < len; i++) {
            args.push('arr[' + i + ']');
        }
        result = eval('context.fn(' + args + ')')
    }

    delete context.fn
    return result;
}
```
- 手写bind
```js
Function.prototype.bind2 = function (context) {

    if (typeof this !== "function") {
      throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
    }

    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    var fNOP = function () {};

    var fbound = function () {
        self.apply(this instanceof self ? this : context, args.concat(Array.prototype.slice.call(arguments)));
    }

    fNOP.prototype = this.prototype;
    fbound.prototype = new fNOP();

    return fbound;

}
```

## 3.什么是 js 闭包

## 4.setTimeout 为何能在单线程的 js 中执行

## 5.详细描述 es5 和 es6 中的作用域

## 6.详细描述异步 EventLoop 机制

## 7.Promise.all 的用法

- 如何让 Promise.all 在抛出异常后依然有效
- Promise 有几种状态
- 如何捕获 promise 错误
- promise 可以串联么
- promise 如何满足多个异步进程的同步顺序
- promise.race 和 promise.all 的区别
- async await
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

\***\*proto\*\***
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

## 24.数组有哪些方法

## 25.事件委托原理

## 26.如何间隔 1s 输出数组元素

## 27.如何实现事件监听

- 有什么区别
