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

- `js`函数中如何绑定`this`到新对象上
- `bind`和`call`有什么区别

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
}
Person.sex = "man";
let person = new Person('name');
console.log(person.name); //name
console.log(person.sex) // undefined  实例无法访问sex属性

console.log(Person.name) //Person 通过构造函数无法访问实例成员
console.log(Person.sex) // man  通过构造函数可以直接访问静态成员
```

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

**__proto__**
每一个`JavaScript`对象都具有一个属性，叫`__proto__`，这个属性会指向该对象的原型

```js
console.log(person.__proto__ === Person.prototype); //true
```

**constructor**
原型指向构造函数是有的:`constructor`,每个原型都有一个`constructor`属性指向相关联的构造函数
```js
console.log(Person === Person.prototype.constructor)  // true
```

当读取实例的属性的时候，如果找不到，就去查找与对象关联的原型种的属性，如果还查找不到，就去原型的原型中去查找
![原型链](/img/dce869109f6474978d24bc1e7da7464a_tplv-t2oaga2asx-watermark.awebp)
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
