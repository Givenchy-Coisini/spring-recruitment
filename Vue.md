## 1.Vue 的父子组件如何通信 非父子组件通信

## 2.什么是 Vuex

- 应用场景
- 原理

## 3.Vuex 中 action 和 mutation 的区别

## 4.详细描述 Vue 中的双向绑定原理

## 5.尽可能的说一下 Vue 的优势

## 6.详细描述一下 Vue 都可以解决什么问题

## 7.virtual dom 如何进行 diff 操作

讲`diff算法`前，先说一下什么是`虚拟dom`吧。
`虚拟DOM`是一个对象，一个什么样的对象呢？`一个用来表示真是DOM的对象`

- 真实`dom`

```html
<ul id="list">
  <li class="item">哈哈</li>
  <li class="item">呵呵</li>
  <li class="item">嘿嘿</li>
</ul>
```

- 对应的虚拟`dom`
```js
let oldVDOM = { // 旧虚拟DOM
        tagName: 'ul', // 标签名
        props: { // 标签属性
            id: 'list'
        },
        children: [ // 标签子节点
            {
                tagName: 'li', props: { class: 'item' }, children: ['哈哈']
            },
            {
                tagName: 'li', props: { class: 'item' }, children: ['呵呵']
            },
            {
                tagName: 'li', props: { class: 'item' }, children: ['嘿嘿']
            },
        ]
    }
```
这个时候我们修改一个`li`标签
```html
<ul id="list">
    <li class="item">哈哈</li>
    <li class="item">呵呵</li>
    <li class="item">fyj哈哈哈哈哈</li> // 修改
</ul>
```
这个时候生成的`新虚拟dom`为
```js
let newVDOM = { // 新虚拟DOM
        tagName: 'ul', // 标签名
        props: { // 标签属性
            id: 'list'
        },
        children: [ // 标签子节点
            {
                tagName: 'li', props: { class: 'item' }, children: ['哈哈']
            },
            {
                tagName: 'li', props: { class: 'item' }, children: ['呵呵']
            },
            {
                tagName: 'li', props: { class: 'item' }, children: ['fyj哈哈哈哈哈']
            },
        ]
    }
```
这就是平常说的`新旧两个虚拟DOM` 这个时候新的虚拟`dom`是数据的最新状态，如果直接拿心得虚拟dom去渲染真实dom的话，效率不会比真是操作dom高

因为中间夹着一个虚拟dom的步骤 所以虚拟dom比真实dom快这句话实错的，或者说是不严谨的。
虚拟dom算法  = 虚拟dom + diff算法

- 什么是diff算法
上面的例子中只有一个li标签修改了文本，其他都是不变的，所以灭有必要所有的节点都更新，只更新这个li标签就行，diff算法就是查出这个li标签的算法

`diff算法就是一种对比算法`。对比两者是旧的虚拟dom 和新的虚拟dom 对比出的结果是哪个虚拟节点更改了 找出这个虚拟节点 并只更新这个虚拟节点对应的真实节点，而不用更新其他数据没有发生改变的节点，实现精准地更新真实dom 进而提高效率

使用虚拟dom算法的损耗：总损耗 = 虚拟dom增删改 + （与diff算法效率有关） 真实dom差异增删改 + 较少的节点的重排和重绘


直接操作真是dom的损耗计算：总损耗 = 真实dom完全增删改+（可能较多的节点）重排重绘

- diff算法的原理
diff同层对比
新旧虚拟dom对比的时候，diff算法比较只会在同层级进行，不会跨层级比较。

当数据改变的时候，会触发setter 并且通过Dep.notify去通知所有的订阅者watcher，订阅者们就会调用patch方法，给真实dom打补丁，更新响应的视图

- patch方法
这个方法的作用就是，对比当前同层的虚拟节点是否为同一种类型的标签
- 是：继续执行patchVnode方法 进行深层对比
- 否：没有必要对比了，直接整个节点替换成新的虚拟节点

- sameVnode方法
patch关键的一步就是sameVnode方法判断是否为同一个类型的节点
key值是否一样
标签名是否一样
是否都为注释节点
是否都定义了data
当标签为input的时候 type是否相同

- patchVnode方法
找到对应的真实dom
判断newVnode 和 oldVnode是否指向同一个对象，如果是，那么直接return
如果他们都有文本节点并且不相等，那么将el的文本节点设置为newVnode的文本节点
如果oldVnode有子节点而newVnode没有，则删除el的子节点
如果oldVnode没有子节点而newVnode有，则将newVnode的子节点真实化之后添加到el
如果两者都有子节点，则执行updateChildren函数比较子节点

场景：因为考虑到大部分的业务场景，并不是所有的子节点的位置都会移动，所以采用这种方式来减小循环的次数

1、头比头
2、尾比尾
3、头比尾
4、如果以上的逻辑都匹配不到，再把所有旧子节点的key做一个映射到旧的节点下标key->index表，然后用新vnode的key去找出在旧节点中可以复用的位置

！！不要用index作为key
## 8.Vue 中 data 为什么是函数不是对象

## 9.Vue 加载完成前隐藏花括号

## 10.es6 的 proxy 是什么

## 11.Vue 生命周期

## 12.Vue 如何监听数据的变化

## 13.了解高阶组件么

## 14.Vue 源码

## 15.Vue-route 原理

## 16.Vue 路由守卫

## 17.vue响应式原理

## 18.v-if原理

## 19.vue和react的区别

## 20.vue patch 原理

## 21.强制刷新当前vue组件
