### flex 布局

#### 什么是flex布局

flex原义为flexible box的意思，意思为弹性布局，为盒子模型提供更好布局方式。

#### 使用原因

传统盒子模型，布局方式：依赖 display 属性 + position属性 + float属性

对于居中、排布等，不易实现，特别是响应式布局。

#### 使用方式
```
.box{
  display: -webkit-flex; /* Safari */
  display: box;
}
.box{
  display: inline-flex;
}
```

> 注意：设为 Flex 布局以后，子元素的float、clear和vertical-align属性将失效。

#### 关于盒子横向和竖向布局、子元素横向竖向布局

容器：水平的主轴（main axis）和垂直的交叉轴（cross axis）
水平开始叫： main start   结束叫： main end
垂直开始叫： cross start  结束叫： cross end

子元素： 默认沿着主轴排列
主轴方向叫：main size
垂直方向叫：cross size

#### 样式属性

容器上样式属性：
```
.box{

  /* 方向属性 默认水平、水平反转、垂直方向、垂直反转 */
  flex-direction: row | row-reverse | column | column-reverse;

  /* 换行属性 不换行、换行、换行反转（第一行在最下面） */
  flex-wrap: nowrap | wrap | wrap-reverse;

  /* flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap */
  flex-flow: <direction> | <wrap>;

  /* 主轴水平对齐方式 头部、尾部、居中、两端对齐，项目之间的间隔都相等、每个项目两侧的间隔相等 */
  justify-content: flex-start | flex-end | center | space-between | space-around;

  /* 垂直交叉轴对齐方式 头部、尾部、居中、两端对齐，项目之间的间隔都相等、每个项目两侧的间隔相等 */
  align-items: flex-start | flex-end | center | baseline | stretch;

  /* 属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。*/
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;

}
```

内部元素上属性
```
.items{

  /* 排序，数字越小越靠前 默认为0 */
  order: 0;

  /* 属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大 */
  flex-grow: 0;

  /* 缩放比例 默认值为1 */
  flex-shrink: 1;

  /* 属性定义了在分配多余空间之前，项目占据的主轴空间（main size） */
  flex-basis: <length> | auto; /* default auto */

  /* 是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto */
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ];

  /* 单个样式可以设置排布布局 */
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
  
}
```
