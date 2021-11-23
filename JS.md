### 1.请详细描述一下 js 的 new 操作

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