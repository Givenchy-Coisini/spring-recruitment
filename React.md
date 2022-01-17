## 1.react的生命周期
**React 生命周期大概分为四个阶段**

- `initialization`初始化阶段
- `mount`挂载阶段
- `updation`更新阶段
- `umounting`卸载阶段

生命周期函数指在某一个阶段组件会自动调用执行的函数

- constructor：构造函数，初始化阶段，定义props and state
- componentWillMount ：组件即将被挂在到页面的时刻执行
- render：页面state或者props发生变化的时候执行   组件挂载中...
- componentDidMount：组件挂载完成的时候执行
- 注意：will and did 只在页面刷新的时候执行一次，而render函数只要有state 或者props 变化就会执行

更新阶段：props属性改变/state状态改变

- shouldComponentUpdate：组件更新之前，自动执行。返回一个布尔值的结果且必须有返回值 return true  唯一用于控制组件重新窜然的生命周期
- componentWillUpdate：组件更新之前，shouldComponentUpdate之后执行 但是如果scu返回false 这个函数就不会执行了
- componentDidUpdate 在组件更新之后执行
- componentWillReceiveProps 子组件接受到父组件传递过来的参数，父组件render函数重新被执行，这个生命周期就会被执行

卸载阶段

- componentWillUnmount 组件从页面中删除的时候执行

**hooks生命周期**

useEffect函数：react首次渲染和之后的每次渲染都会调用一边useEffect函数，而之前我们要用两个生命周期（componentDidMount/conponentDidUpdate）

useEffect是异步执行的，而类组件中的是同步执行的

要实现componentWillMount效果，useEffect的第二个参数是要提到的，它是一个数组，数组中可以写入很多状态对应的变量，当状态值发生变化的时候，
## 2.react高阶组件
- 高阶组件本身不是组件，它是一个参数为组件，返回值为组件的一个函数，高阶组件用于强化组件，复用逻辑，提升渲染等作用。
- 如果一个函数接受一个或者多个函数作为参数或者返回一个参数就可以称为高阶函数。
- 高阶组件解决了哪些问题
  - 复用逻辑 高阶组件更像一个加工react组件的工厂，批量对原有组件进行加工，包装处理。我们可以根据业务需求定制化专属hoc这样可以解决复用逻辑。
  - 强化props 这个是hoc最常用的用法之一，高阶组件返回的组件，可以劫持上一层传过来的props，然后混入新的props，来增强组件的功能。
  - 赋能组件，hoc有一项独特的特性，就是可以给被hoc包裹的业务组件，提供一些拓展的功能，需要和业务组件紧密结合。
  - 控制渲染：劫持渲染是hoc一个特性，在包装组件中，可以对原来的组件，进行条件渲染，节流渲染，懒加载等功能。