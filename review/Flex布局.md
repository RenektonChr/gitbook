# Flex——弹性盒子布局

## Flex简介

布局的传统解决方案是基于盒模型（标准盒模型、怪异盒模型）的，依赖`display属性`、`position属性`、`float属性`。这种方案对于特殊的布局非常不友好，比如居中就 不易实现。Flex布局可以简便、完整、响应式的实现各种页面。目前，Flex布局已经得到了所有浏览器的支持，这意味着，现在就可以放心的使用这项功能。

## Flex布局是什么？

Flex是Flex Box的缩写，意为`弹性盒子`，用来为盒状模型提供最大的灵活性。

任何一个容易都可以指定为Flex布局（弹性盒子）。

```css
.box {
  display: flex;
}
```

行内元素也可以使用Flex布局：

```
.box {
	display: inline-flex;
}
```

> 注意：
>
> 设为Flex布局之后，子元素的float、clear和vertical-align属性将失效。

## Flex基本概念

采用Flex布局的元素，成为Flex容器（Flex Container），简称`"容器"` 。它的所有子元素自动成为容器成员，称为Flex项目（Flex Item），简称`"项目"`。

容器默认存在两个轴：主轴（main axis）——默认是水平轴，交叉轴（cross axis）——默认是垂直与水平方向的纵向轴。主轴的开始位置叫做main start，结束的位置叫main end；交叉轴开始的位置叫做cross start，结束位置叫做cross end。

> 项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据交叉轴的空间叫做cross size。

## 容器的属性

以下6个属性设置在容器上。

+ flex-direction
+ flex-wrap
+ flex-flow
+ justify-content
+ align-items
+ align-content



### flex-direction属性

**`flex-direction`**属性决定主轴的方向（即项目的排列方向）。

基本语法格式如下：

```css
.box {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

它可能有四个值：

+ row（默认值）：主轴为水平方向，起点在左端。
+ row-reverse：主轴为水平方向，起点在右端。
+ column：主轴为垂直方向，起点在上沿。
+ column-reverse：主轴为垂直方向，起点在下沿。

### flex-wrap属性

默认情况下，项目都排在一条线（又称"轴线"）上。flex-wrap属性定义，如果一条轴线排不下，如何换行。

基本语法格式如下：

```css
.box {
	flex-wrap: nowrap | wrap | wrap-reverse;
}
```

+ nowrap（默认值）：不换行。
+ wrap：换行，第一行在上方。
+ wrap-reverse：换行第一行在下方。

### flex-flow属性

flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。

```css
.box {
	flex-flow: <flex-direction> <flex-wrap>
}
```

### justify-content属性

 justify-content属性定义了项目在主轴上的对齐方式。

基本语法格式如下：

```css
.box {
  justify-content: flex-start | flex-end | center | space-between | space-around;
}
```

对齐方式与主轴的方向有关，当主轴沿水平方向从左到右时，具体含义为：

+ flex-start（默认值）：左对齐。
+ flex-end：右对齐
+ center：居中
+ space-between：两端对齐，项目之间的间距相等。
+ space-around：每个项目两侧的间隔相等。所以项目之间的间隔比项目与边框之间的间隔大一倍。

### align-items属性

align-items属性定义项目在交叉轴上的对齐方式。

基本语法如下：

```css
.box {
	align-items: flex-start | flex-end | center | baseline | stretch;
}
```

对齐方式的方向与交叉轴的方向有关，当交叉轴的方向是竖直方向的时候，具体含义为：

+ flex-start：交叉轴的`起点`对齐。
+ flex-end：交叉轴的`终点`对齐。
+ center：交叉轴的`中点`对齐。
+ baseline：项目的第一行文字的基线对齐。
+ stretch（默认值）：如果项目未设置高度或者设置为`auto`，将占满整个容器的高度。

### align-content属性

用于控制多行项目的对齐方式，`如果项目只有一行则会不起作用`，注意当有多行时，定义了align-content后，align-items会失去作用。默认stretch，即在项目没有设置高度，或者高度为auto的情况下让项目填满整个容器，与align-items类似。

基本语法如下：

```css
.box {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

+ flex-start：与交叉轴的起点对齐。
+ flex-end：与交叉轴的终点对齐。
+ center：与交叉轴的中点对齐。
+ space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
+ space-around：每根轴线的两侧间隔相等。
+ stretch（默认值）：轴线占满整个交叉轴。

总结：

flex-start、flex-end、center与align-items属性表现一致。

space-around与justify-content属性的表现一致。

## 项目属性

容器属性是加载容器上的，项目属性是加在项目上的。以下6个属性设置在项目上：

+ order
+ flex-grow
+ flex-shrink
+ flex-basis
+ flex
+ align-self

### order属性

order的值是整数，默认为0，整数越小，item排列越靠前，用于决定项目的排列顺序。取值越大，item的排序越靠后，可以取负值。

基本语法格式如下：

```css
.item {
	order: <interger>;
}
```

### flex-grow属性

flex-grow属性定义项目的放大比例，默认为0，即如果不存在剩余空间，也不放大。

基本语法格式如下：

```css
.item {
  flex-grow: <number>;
}
```

如果项目的flex-grow属性都为1，则他们将平分剩余空间——当然得存在的情况下才行。如果一个项目的flex-grow为2，其他项目都为1，则前者占据的剩余空间将比其他项目多一倍（实际上就是 比例关系）。

### flex-shrink属性

flex-shrink属性定义了项目的缩小比例，默认为1，及如果在空间不足的情况下，该项目将缩小。负值对该属性无效。

基本语法格式如下：

```css
.item {
	flex-shrink: <number>;
}
```

### flex-basis属性

flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间。浏览器根据这个属性计算主轴是否有多余空间。他的默认值是auto，即项目本来的大小。

基本语法格式如下：

```css
.item {
  flex-basis: <length> | auto;
}
```

它可以设置为跟width或height一样的值（比如300px），则项目将占据固定空间。

### flex属性

flex属性是flex-grow、flex-shrink、flex-basis属性的简写，默认值为 0 1 auto。后两个属性可选。

建议使用简写属性。

### align-self属性

align-self属性允许单个项目有与其他项目不同的交叉轴对齐方式。取值与align-items完全一样，可覆盖align-items属性。



























