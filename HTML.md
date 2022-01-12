## 1.伪类和伪元素的区别

> 伪元素在`css3`之前就已经存在，只是没有伪元素的说法，都归纳为伪类。

- 伪类表示被选择元素的某种状态，例如:hover
- 伪元素表示的是被选择元素的某个部分，这个部分看起来像一个独立的元素，但是是”假元素“，只存在于`css`中,所以叫伪元素，例如:before 和 :after
- 核心区别就在于是否创建了“新的元素”

## 2.px/rpx/em/rem/vh/vw/vm/%的区别

- px就是像素，是设备或者图片中最小的一个点，1024*768表示的是水平方向是1024个像素点，垂直方向是768个像素点，px是网页设计中常用的单位，也是基本单位，通过px可以设置固定的布局或者大小，缺点是没有弹性。 
- rpx是微信小程序解决自适应屏幕尺寸的单位，可以根据屏幕宽度进行自适应，微信小程序规定屏幕的宽度是750px。例如宽度，相当于把屏幕分成750份，1份就是1rpx，而375px 的屏幕=>1px===2rpx
- em参考物是父元素的`font-seze`,具有继承的特点，如果自身定义了`font-size`就按照自身来计算（浏览器默认字体是16px）特点是1em的值不是固定的 因为em会继承父元素的字体大小 1em=1倍父元素字体大小 2em=2倍父元素字体大小
- rem是相对于根元素html，我们只需要在根元素确定一个参考值，可以设计html大小为10px，到时候设置1.2rem就是12px 优点是：只需要设置根目录大小就可以把整个页面的成比例调整好
- vh css3新单位 view height的简写，是指可视窗口的高度，假设高度是1200px 的话。那么100vh就是1200px 浏览器高度如果是900px的话，1vh=900px/100 = 9px
- vw css3新单位 view width的简写。是指可视窗口的宽度，假设宽度是1200px的话，那100vw就是1200px 浏览器宽度如果是1200px的话，1vw=12px
- vm css3 新单位 相对于视口的高度或者宽度中较小的那个，高度900px 宽度1200px 1vm=900px/100 = 9px
- % 对于普通定位元素就是我们理解的父元素  对于position:absloute 的元素相对于已经定位的父元素 对于position：fixed 的元素相对于viewport（可视窗口）

## 3.移动端适配

- 什么是分辨率？
  - 每个像素的大小是不固定的，它是依据设备的分辨率决定的。分辨率是指纵横向上的像素点数，单位是px 相同大小的屏幕 分辨率低时，在屏幕上显示的像素少，单个像素尺寸比较大，屏幕分辨率高时，在屏幕上显示的像素多，单个像素尺寸较小。
- 1px边框问题
  - css写1px的时候，由于它是逻辑像素，导致根据设备像素比dpr去映射到设备上就为2px 3px 每个设备的屏幕尺寸不一样  导致每个物理像素渲染出来的大小也不同
- viewport
  - 视口 ：通常与浏览器的窗口相同
  - meta标签：
    - width：定义视口的宽度 单位是像素  取值是正整数或者设备宽度 device-width
    - height：定义视口的高度 单位是像素 取值是正整数或者device-height
    - initial-scale：定义初始缩放值 取值是整数或者小数
    - minimum-scale 定义缩小最小比例 它必须小于或者等于maximum-scale设置
    - maximum-scale：定义放大最大比例，它必须大于或者等于minimum-scale设置。取值为正数或者小数
    - user-scalable 定于是否允许用户手动缩放页面，默认值是yes 设置为no的时候可以解决移动端点击事件延迟问题
  - rem适配
    - rem是相对于html节点的font-size来做计算的，所以在页面初始化的时候给根元素设置一个font-size，接下来的元素就根据rem来布局，这样可以保证在页面大小变化时候，布局可以自适应
  - vh vw 布局
    - px转换cw的时候不一定能完全整除，因此有一定的像素差 webpack解析css 的时候用postcss-loader 用postcss-px-to-viewport能自动实现px到vw的转化
  - 移动端适配的流程
    - 在head设置width=device-width 的viewport
    - 在css中使用px
    - 在适当的场景使用flex布局，或者配合vw进行自适应
    - 在跨设备类型的时候使用媒体查询
    - 在跨设备类型如果交互差异太大的情况，考虑分开项目开发
  - flex布局
    - 先说一下float布局
      - 浮动元素会生成一个块级框，而不论它本身是何种元素
      - 浮动的副作用有很多
        - 当子元素都设置了浮动时，就会导致父元素塌陷，即父元素撑不开
        - 无法实现动态实现自适应布局
        - 由于浮动导致父级子级之间设置了css padding css margin属性的值不能正确表达 特别是上下遍的margin和padding不能正确显示
        - 元素一脱离文档流会导致元素二上去了
    - 容器---display :flex
      - flex容器属性（父）
        - flex-direction
          - row：主轴为水平方向，起点在左端
          - row-reverse：主轴为水平方向，起点在右端
          - column：主轴为垂直方向，起点在上沿
          - column-reverse：主轴为垂直方向，起点在下沿
        - flex-wrap
          - nowrap：项目排在一条线上不换行
          - wrap：换行，第一行在上方
          - wrap-reverse：换行，第一行在下方
        - flex-flow
          - 是flex-direction和flex-wrap的简写 默认值是row nowrap
        - justify-content
          - 定义子元素在主轴上的对齐方式
          - flex-start：默认值左对齐
          - flex-end：右对齐
          - center：居中
          - space-between：两端对齐，项目之间的间隔都相等
          - space-round：每个项目两侧的间隔相等，所以，项目之间的间隔比项目于边框的间隔大一倍
        - align-items
          - 定义项目在交叉轴上如何对齐
          - flex-satrt：交叉轴的起点对齐
          - flex-end：交叉轴的终点对齐
          - center：居中对齐
          - baseline：子元素的第一行文字的基线对齐
          - stretch：子元素没有设置高度或者设置为auto，将占满整个容器的高度
        - align-content
          - 定义了多根轴线的对齐方式，如果项目只有一根轴线，该属性不起作用
          - flex-start：与交叉轴的起点对齐
          - flex-end：与交叉轴的终点对齐
          - center：与交叉轴的中点对齐
          - space-between：与交叉轴的两端对齐，轴线之间的间隔平均分布
          - space-around：每个项目两侧的间隔相等，所以，项目之间的间隔比项目于边框的间隔大一倍
          - 今天stretch：子元素没有设置高度或者设置为auto，将占满整个容器的高度
      - flex项目属性（子元素）
        - order：定义项目的排列顺序，数值越小，排列越靠前，默认为0
        - flex-grow：定义项目的放大比例，默认值为0 即如果存在剩余空间，也不放大  所有都为1 则平分剩余空间 一个为2 其他为1 则前者占据的空间要比其他项多一倍
        - flex-shrink 缩小比例。负值无效 默认为1。如果空间不足，该项目将缩小
        - flex-basis：在分配对于空间之前，项目占据主轴的空间，默认值是auto。可以设置为100px
        - flex：flex-grow flex-shrink flex-basis简写   默认值为0 1 auto
        - align-self：auto flex-start flex-end center baseline stretch

## 4.position的所有可能值

static：position的默认值，没有定位，元素出现在正常的流中，忽略top/z-index等声明

relative：元素相对于自身其正常位置进行定位，不会影响常规流中的任何元素

absolute：元素相对于离自身最近的不是static的元素进行定位，如果没有定位的祖先元素，则一直追溯到body元素。

fixed：相对于浏览器窗口进行定位，和absolute一致，但是当出现滚动条的时候，对象不会随着滚动

inherit：规定应该从父元素继承position属性的值

sticky：根据用户滚动位置进行定位，起先会被相对定位，直到在视口中遇到给定的偏移位置为止，然后粘贴在适当的位置。

## 5.什么是shadow Dom？有什么用？

Shadow dom 属于web components技术中的一环，是一个极其关键的接口实现，shadow dom接口可以将一个隐藏的，独立的dom附加到一个元素上，操作shadow dom和操作普通dom是一样的，shadow dom内部可以添加任意dom元素，shadow dom内部的元素始终不会影响到它外部的元素，例如添加style 只会对shadow dom内部元素生效

- shadow host ：一个常规的dom节点，shadow dom会被附加到这个节点上
- shadow tree： shadow dom内部的dom树
- shadow boundary：shadow dom结束的地方 也是常规dom开始的地方
- shadow root ：shadow tree 的根节点

外部css中被应用在shadow host 元素的样式中 能被继承的属性都会被shadow dom中的元素继承，通过选择器无法直接对shadow dom的元素应用样式。

shadow dom 中style 或者引入的css 不会对外部的元素造成影响

：focus-within 是一个css伪类，表示一个元素获得焦点，或该元素的后代元素获得焦点，即便是shadow dom 当其获得焦点的时候，也会使得父级dom元素上的focus-within生效

## 6.svg和cavans的区别

- cavans绘画出来的图形一般称为位图，也就是放大缩小的情况下会出现失真的情况，svg绘制的图形是矢量图，不存在失真的情况
- cavans绘制的图形不会出现在dom结构中，svg绘制的会存在于dom结构
- cavans类似于动画，每次图形的改变都是先清除原来的图形，然后把新的图形画上去，svg则是可以直接通过js来进行某些操作
- cavans依赖于分辨率，svg不依赖分辨率
- cavans最适合图像密集型的游戏，其中许多对象会被频繁重绘，svg不适合游戏应用

## 7.dom渲染的性能损耗在哪里

- dom操作影响性能最主要是因为它导致了浏览器的重绘和重排
- 重绘：就是指页面某些部分需要重新绘制，由于节点的几何属性发生改变或者由于样式发生改变，例如改变元素背景颜色时，屏幕上的部分内容需要更新，而元素的位置和尺寸并没有改变。
- 重排：元素的位置或者尺寸发生了改变，浏览器需要重新计算渲染树，导致渲染树一部分或者全部发生变化，渲染树重新建立以后，浏览器会重新绘制页面上受影响的元素。也就是说，重排改变的是dom文档的结构，例如dom元素的位置或者尺寸发生了变化，需要重新布局，就是发生重排。

## 8.如何从1000个div中高效的删除10个div

- 一次性插入1000个div，如何优化插入的性能
  - 使用Fragment

```
    var fragment = document.createDocumentFragment();
    fragment.appendChild(elem);
```

- 向1000个并排的div元素中，插入一个平级的div元素，如何优化插入的性能
  - 先display:none 然后插入 再display:block
  - 赋予key，然后使用virtual-dom，先render，然后diff，最后patch
  - 脱离文档流，用GPU去渲染，开启硬件加速

## 9.如何监听img加载完成

- load事件

  ```js
  img1.onload = function() {
  p1.innerHTML = 'load'
  }
  ```

- readystatechag事件

  ```js
  img1.readtstatechange () {
    if(img1.readyState == 'complete' || img1.readyState == 'loaded') {
      p1.innerHTML ='readystatechange:loaded'
    }
  }
  ```

- img 的 compelete属性

  ```js
  function imgLoad(img,callback) {
    var time = setInterval(function(){
      if(img.compelete) {
        callback(img)
        clearInterval(timer)
      }
    },50)
  }
  imgLoad(img1,function() {
    p1.innerHTML('加载完毕')
  })
  ```

## 10.块级元素和行内元素

- 块级元素会独占一行，其宽度自动填满其父元素宽度
- 行内元素不会独占一行，相邻的行内元素会排列在同一行里，直到一行排不下，才会换行，其宽度随元素的内容而变化（元素撑开）
- 块级元素可以设置宽高，块级元素即使设置了宽度，仍然是独占一行的
- 行内元素设置宽高无效
- 块级元素可以设置margin 和 padding
- 行内元素的水平方向的margin和padding是可以产生边距效果的，但是竖直方向的margin和padding不会产生边距效果
- 块级元素可以包含行内元素和块级元素，行内元素不能包含块级元素
- 行内块元素 和其他元素同一行，可以设置元素的宽高等

## 11.标签语义化

- 简单来说就是用正确的标签做正确的事情
- 头部header 导航nav 主体内容main 标题h1-h6 段落p 侧边栏aside 页脚footer
- 网页加载慢导致css文件还没有加载的时候，页面仍然清晰，可读，好看
- 提升用户体验，例如title/alt 可用于解释名词或者解释图片信息
- 有利于seo，和搜索引擎建立良好沟通，有助于爬虫抓取更多的有效信息
- 方便其他设备（如屏幕阅读器/盲人阅读器/移动设备）更好的解析页面
- 使代码具有可读性，便于团队开发和维护

## 12.盒模型

- 盒子的主要属性是
  - width 内容的宽度（不是盒子的宽度）
  - height 内容的高度（不是盒子的高度）
  - padding 内边距
  - border 边框
  - margin 外边距
- 盒模型主要有标准盒模型和ie盒模型

