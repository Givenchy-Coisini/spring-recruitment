## 1.请详细描述一下 js 的 new 操作

一句话介绍`new`

`new`运算符创建一个用户定义的对象类型的实例或者具有构造函数的内置对象的实例
```js
function Person(name,age) {
  this.name = name
  this.age = age
}
Person.prototype.sayName = function() {
  console.log(this.name)
}
let fyj = new Person('fyj',21)
console.log(fyj) // Person {name:'fyj', age:21}
fyj.sayName() // 'fyj'
```

`new`通过Person创建出来的实例可以访问到构造函数的属性
`new`通过Person创建出来的实例可以访问到构造函数原型链上的方法和属性

在构造函数内加上返回值是什么呢？
**1、返回基本类型**
```js
function Car (price) {
  this.price = price
  return 20
}
let bigCar = new Car(90)
console.log(bigCar.price) // 90
```
返回值是原始值的时候，那么这返回值没有任何意义
**2、返回引用数据类型**
```js
function Car(price) {
  this.price = price
  return {km:200}
}
let bigCar = new Car(90)
console.log(bigCar.price,bigCar.km)  //undefined ,200
```
返回值是引用数据类型的时候，那么这个返回值就会被正常使用

**new的实现**
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
## 2.this指向问题
- `js`函数中如何绑定`this`到新对象上
- `bind`和`call`有什么区别
## 3.什么是js闭包
## 4.setTimeout为何能在单线程的js中执行
## 5.详细描述es5 和es6 中的作用域
## 6.详细描述异步EventLoop机制
## 7.Promise.all的用法
- 如何让Promise.all在抛出异常后依然有效
- Promise有几种状态
- 如何捕获promise错误
- promise 可以串联么
- promise如何满足多个异步进程的同步顺序
- promise.race 和 promise.all的区别
- async await
- 如何顺序执行10个异步任务
## 8.详述节流和防抖
- 手写节流和防抖
## 9.简述es6的新特性
## 10.简述箭头函数的特性
## 11.为什么需要深拷贝
- 什么是浅拷贝
## 12.简述指针是什么
## 13.如何监控未处理的异常
## 14.纯js如何获取scrolltop的值
## 15.arguments 如何转数组
## 16.移动端和pc端click事件为什么差了300ms
## 17. == 和 ===区别
## 18. 原型和原型连是什么
## 19.三句话概括所有值传递类型、引用传递类型 以及如何用引用的方式传递值类型
## 20.js基础类型
- null undefined 区别
- 怎么比较
## 21.指针和引用的区别
## 22.js中对于不同环境的变量什么时候释放
- 在非闭包的情况下变量什么时候会被回收
## 23.js继承说一下
## 24.数组有哪些方法
## 25.事件委托原理
## 26.如何间隔1s输出数组元素
## 27.如何实现事件监听
- 有什么区别