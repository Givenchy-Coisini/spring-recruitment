## 1.用 css 写无限循环动画

```html
<!DOCTYPE html>
<!--两端相同的文本。 移动50%的距离  图片的话在最后一张图片的后面添加一张与第一张图片相同的元素即可-->
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>无限轮播文字</title>

    <style>
      body {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
      }

      #wrap {
        overflow: hidden;
        position: relative;
        width: 600px;
        height: 20px;
        padding-bottom: 5px;
        border-bottom: 1px solid #cccccc;
        white-space: nowrap;
      }

      #slide {
        position: absolute;
        animation: slide 10s linear infinite; // 关键语句
      }

      @keyframes slide {
        0% {
          transform: translateX(0);
        }
        100% {
          transform: translateX(-50%);
        }
      }
    </style>
  </head>
  <body>
    <div id="wrap">
      <div id="slide">
        <span
          >故事的小黄花 从出生那年就飘着 童年的荡秋千 随记忆一直晃到现在 Re So
          So Si Do Si La So La Si Si Si Si La Si La So</span
        >
        <span
          >故事的小黄花 从出生那年就飘着 童年的荡秋千 随记忆一直晃到现在 Re So
          So Si Do Si La So La Si Si Si Si La Si La So</span
        >
      </div>
    </div>
  </body>
</html>
```

## 2.如何清除 float

- 清除浮动并不是真正意义上的清除浮动，而是清除浮动对其他元素的影响

- ```css
  clear: left/right/both
    // 告诉被影响的兄弟元素 左边或者右边不允许有浮动元素的存在可以清除浮动的影响;; ; ; ; ; ; ;
  ```

- 在父元素标签结束之前插入清除浮动的块级元素 clear:box

- 利用伪元素 clearfix

  ```css
  .clearfix:after {
    content: "";
    height: 0;
    display: block;
    clear: both;
  }
  // 伪元素是在被选元素的内容后面插入内容
  // 在父元素的最后添加了一个:after伪元素 通过清除伪元素的浮动 达到撑起父元素高度的目的，必须是一个块级元素(不可见)
  ```

- overflow ：不是 visible。构成 bfc。bfc 的高度是要包括浮动元素的 所以父级元素的高度可以被撑起来，达到了清除浮动的影响

## 3.列举几个 css 中可继承和不可继承的元素

- css 继承可以定义为特定的 css 属性向下传递到子孙元素，就是指被包在内部的标签将拥有外部标签的样式，即子元素可以继承父元素的属性。
- 能继承的属性
  - 字体系列属性：font font-family font-weight font-size font-style
  - 文本系列属性：color line-height word-spacing letter-spacing text-transform text-indent text-align
  - 元素可见性：visibility
  - 光标属性：cursor
  - ....
- 不能继承的属性
  - display
  - 文本属性：vertical-align text-decoration
  - 盒子模型的属性：width height margin border padding
  - 背景属性：background background-color background-image
  - 定位属性：float position ...
  - .....

## 4.用 css 选中列表的第二项

```css
// first-child  用于选择属于父元素的首个子元素的指定选择器
// last-child 最后一个
// nth-child(3) 表示选择第三个标签
// nth-child(2n) 表示选择列表的偶数标签
// nth-child(2n-1) 表示选择列表的奇数标签
// nth-child(n+3) 从第三个开始一直到最后
// nth-child(-n+3) 0到3个
// nth-last-child(3) 倒数第3个标签
```

## 5.什么情况下 css 会使用 gpu 加速

- 3d 的渲染流程是：
  - 计算顶点数据，构建 3d 图形
  - 给每个三角形贴图，画上纹理
  - 投影到二维的屏幕，计算每个像素的颜色(光栅化)
  - 把每一帧的数据写入显存的帧缓冲区
- 处理 3d 渲染的过程中，由于顶点的数量特别庞大，所以出现了专门用于这种 3d 数据的并行计算的硬件 称为 gpu
- 和 cpu 的一个一个数据计算不同，gpu 是并行的，有成百上千个核心用于并行计算
- cpu 是通用的，能狗执行各种逻辑和运算，但是 gpu 可以处理并行大量重复任务，不能处理复杂逻辑，也就是说如果逻辑复杂，那么只能用 cpu，如果只是计算量大，并且每个计算都比较重复，那就比较适合 gpu
- css 大部分样式还是 cpu 来计算的，但是一些 3d 样式和动画可以交给 gpu 来跑
- transform opacity filter will-change gpu 硬件加速是要新建图层的，而把元素移动到新图层是个耗时的操作，界面可能会闪一下，will-change 就是提前告诉浏览器在一开始就要把元素放到新的图层，方便后面用 gpu 渲染的时候，不需要做图层的新建。
- gpu 加速可以减轻 cpu 的压力，使得渲染更加流畅，但是也会增加内存的使用，对于 transform opacity filter 默认会开启硬件加速
- gpu 硬件加速在移动端也是有很大的用处，可以有效的减少资源的利用

## 6.css filter 是什么

- 能够实现一些滤镜效果，如灰阶，高斯模糊等。
- 网页虽然不是图片，但是 i 可以利用图片滤镜是因为网页在渲染到屏幕之前都会被栅格化成 cavans 位图然后再画到屏幕上，所以 filter 处理的就是这张位图。
- css 中我们不用借助任何软件就可以实现多种滤镜效果，css 滤镜还可以对网页中的元素或者视频进行处理。
- 通过 filter 属性并配合一些函数来实现
  - none 没有效果
  - blur(px) 高斯模糊 默认值是 0 值越大越模糊
  - brightness(100%) 图像亮度，默认原始图像 100% 0%代表没有亮度图片全黑 超过 100% 图像将变得更亮
  - contrast 对比度
  - grayscale(%) 将图片变为灰度图像
  - invert 100%代表完全反转
  - opacity 不透明度默认 100% 0% 代表完全透明
  - inherit 从父元素继承此属性的值

## 7.什么是 sass 和 less

- sass 和 less 都是 css 预处理器，是一种特殊语法/语言，最终会编译成为 css
- Less 和 sass 有相同之处
  - 混入-class 中的 class
  - 参数混入 可以传递参数的 class 就像函数一样
  - 嵌套规则 class 中嵌套 class 从而减少重复的代码
  - 运算-css 中用上数学
  - 颜色功能-可以编辑颜色
  - 名字空间-分组样式，从而可以被调用
  - 作用域-局部修改样式
  - js 赋值-在 css 中使用 js 表达式赋值
- less 和 sass 的区别
  - less 基于 js 实现 是在客户端处理的，sass 是基于 ruby 实现的，在服务端处理的
  - 关于变量的唯一区别是 less 用@ sass 用$
  - // less 注释方式 不会被编译 /\*\*/会被编译
  - less 解析 js 会比 sass 慢一点

## 8.css 动画的最小间隔

一般浏览器的刷新频率为每秒 60 次，所以最小时间间隔为 1/60\*1000 约为 16.7ms

## 9.垂直水平居中

- 行内元素水平居中

```html
<div class="container">
  <div class="box"></div>
</div>

<style>
  .container {
    text-align: center;
    width: 100%;
    height: 300px;
    background: #ccc;
  }
  .container .box {
    width: 100px;
    height: 100px;
    background: pink;
    display: inline-block; // 转成行内块 就可以使用行内元素水平居中了
  }
</style>
```

- margin 实现水平居中

```html
<div class="container">
  <div class="box"></div>
</div>

<style>
  .container {
    width: 100%;
    height: 300px;
    background: #ccc;
  }
  .container .box {
    width: 100px;
    height: 100px;
    background: pink;
    margin: 0 auto;
  }
</style>
```

- flex 多个块级元素水平居中

```html
<div class="container">
  <div class="box"></div>
  <div class="box"></div>
  <div class="box"></div>
</div>

<style>
  .container {
    width: 100%;
    height: 300px;
    background: #ccc;
    display: flex;
    justify-content: center;
  }
  .container .box {
    width: 100px;
    height: 100px;
    background: pink;
    margin-left: 10px;
  }
  .container .box:first-child {
    margin-left: 0;
  }
</style>
```

- 垂直居中

  - 行高等于高度
  - padding：100px 0；（上下一半）
  - display:flex. align-items:center;
  - 绝对定位 已经知道宽高 margin-top：-50px top:50%;
  - 绝对定位 不知道宽高 top:50%. transform:translateY(-50%)

- 水平垂直居中

```html
<div class="container">
  <div class="box"></div>
</div>

<style>
  .container {
    width: 100%;
    height: 300px;
    background: #ccc;
    line-height: 300px;
    text-align: center;
  }
  .container .box {
    width: 100px;
    height: 100px;
    background: pink;
    display: inline-block;
    vertical-align: middle;
  }
</style>
```

- 绝对定位实现水平垂直居中(需要知道子元素的宽高)

```html
<div class="container">
  <div class="box"></div>
</div>

<style>
  .container {
    width: 100%;
    height: 300px;
    background: #ccc;
    position: relative;
  }
  .container .box {
    width: 100px;
    height: 100px;
    background: pink;
    position: absolution;
    top: 50%;
    left: 50%;
    margin-top: -50px;
    margin-left: -50px;
    // 假如不知道宽度和高度
    transform: translate(-50%, -50%);
    // 四边定位为0，margin为auto （子元素高度未知时无效）
  }
</style>
```

- flex 布局

```html
<style>
  .container {
    //...
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
```

## 10.css 优先级

- 当创建的样式表越来越复杂的时候，一个标签样式将会受到越来越多的影响，这种影响可能来自周围的标签，也可能来自其自身。
- css 优先级是分配给指定的 css 声明的一个权重，它由匹配选择器中的每一种选择器类型的数值决定
- !important>行内样式(1000)>id 选择器(100)>类选择器(10)=伪类选择器=属性选择器>标签(1)=伪元素选择器>通配符>继承>浏览器默认属性

## 11.说一下 BFC

- sass 和 less 都是 css 预处理器，是一种特殊语法/语言，最终会编译成为 css
- Less 和 sass 有相同之处
  - 混入-class 中的 class
  - 参数混入 可以传递参数的 class 就像函数一样
  - 嵌套规则 class 中嵌套 class 从而减少重复的代码
  - 运算-css 中用上数学
  - 颜色功能-可以编辑颜色
  - 名字空间-分组样式，从而可以被调用
  - 作用域-局部修改样式
  - js 赋值-在 css 中使用 js 表达式赋值
- less 和 sass 的区别
  - less 基于 js 实现 是在客户端处理的，sass 是基于 ruby 实现的，在服务端处理的
  - 关于变量的唯一区别是 less 用@ sass 用$
  - // less 注释方式 不会被编译 /\*\*/会被编译
  - less 解析 js 会比 sass 慢一点

## 12.实现三栏布局

**三栏布局就是两边固定，中间自适应**

- 浮动布局

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Layout</title>
    <style media="screen">
      html * {
        padding: 0;
        margin: 0;
      }
      .layout article div {
        min-height: 150px;
      }
    </style>
  </head>
  <body>
    <!--浮动布局  -->
    <section class="layout float">
      <style media="screen">
        .layout.float .left {
          float: left;
          width: 300px;
          background: red;
        }
        .layout.float .center {
          background: yellow;
        }
        .layout.float .right {
          float: right;
          width: 300px;
          background: blue;
        }
      </style>
      <h1>三栏布局</h1>
      <article class="left-right-center">
        <div class="left"></div>
        <div class="right"></div>
        // 右栏部分要写在中间内容之前
        <div class="center">
          <h2>浮动解决方案</h2>
          1.这是三栏布局的浮动解决方案； 2.这是三栏布局的浮动解决方案；
          3.这是三栏布局的浮动解决方案； 4.这是三栏布局的浮动解决方案；
          5.这是三栏布局的浮动解决方案； 6.这是三栏布局的浮动解决方案；
        </div>
      </article>
    </section>
  </body>
</html>
```

- dom 结构必须先写浮动部分，然后再中间块，否则右浮动块会掉到下一行。浮动布局有局限性，浮动元素脱离文档流，要清除浮动，这个处理不好的话，会带来很多问题，比如父容器高度塌陷等。

- 绝对定位

```html
<!--绝对布局  -->
<section class="layout absolute">
  <style>
    .layout.absolute .left-center-right > div {
      position: absolute; //三块都是绝对定位
    }
    .layout.absolute .left {
      left: 0;
      width: 300px;
      background: red;
    }
    .layout.absolute .center {
      right: 300px;
      left: 300px; //离左右各三百
      background: yellow;
    }
    .layout.absolute .right {
      right: 0;
      width: 300px;
      background: blue;
    }
  </style>
  <h1>三栏布局</h1>
  <article class="left-center-right">
    <div class="left"></div>
    <div class="center">
      <h2>绝对定位解决方案</h2>
      1.这是三栏布局的浮动解决方案； 2.这是三栏布局的浮动解决方案；
      3.这是三栏布局的浮动解决方案； 4.这是三栏布局的浮动解决方案；
      5.这是三栏布局的浮动解决方案； 6.这是三栏布局的浮动解决方案；
    </div>
    <div class="right"></div>
  </article>
</section>
```
容器脱离了文档流，后代元素也脱离了文档流，高度未知的时候，会有问题

- flex布局
  - 左边300px 右边300px 中间flex:1
