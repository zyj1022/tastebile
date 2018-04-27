
# Flex 布局属性详解

## 简介

通常我们在制作html页面的时候，都是基于盒模型，在2009年，W3C提出了一种新的方案——Flex布局，可以简便、完整、响应式地实现各种页面布局。目前，浏览器的支持情况IE10+及现代浏览器都支持，那么是否该尝试这种新式布局方法呢。

## Flex布局是什么？

Flex是Flexible Box的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。
任何一个容器都可以指定为Flex布局。

```
.box{
  display: flex;
  display: -webkit-flex; //
  display: inline-flex; // 行内元素也可以使用Flex布局
}
```
**注意，设为Flex布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效。**


## 基本概念

采用Flex布局的元素，称为Flex容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为Flex项目（flex item），简称"项目"。

![flex-container](http://og8z552x2.bkt.clouddn.com/flex-container.png)

容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。
项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`


## [容器的6个属性](https://workflowy.com/s/Ceay.AN3QiSR6pK)

- 1、flex-direction 属性决定主轴的方向（即项目的排列方向）

	* row (默认值) 主轴为水平方向，起点在左端。
	* row-reverse 主轴为水平方向，起点在右端。
	* column 主轴为垂直方向，起点在上沿
	* column-reverse 主轴为垂直方向，起点在下沿

- 2、flex-wrap 属性定义，如果一条轴线排不下，如何换行

	* nowrap (默认) 不换行
	* wrap 换行，第一行在上方
	* wrap-reverse 换行，第一行在下方

-  3、flex-flow 属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap

- 4、justify-content 属性定义了项目在主轴上的对齐方式

	* flex-start  (默认) 左对齐
	* flex-end 右对齐
	* center 居中
	* space-between 两端对齐，项目之间的间隔都相等
	* space-around 每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍

- 5、align-items 属性定义项目在交叉轴上如何对齐

	* stretch  (默认) 如果项目未设置高度或设为auto，将占满整个容器的高度
	* flex-start 交叉轴的起点对齐
	* flex-end 交叉轴的终点对齐
	* center 交叉轴的中点对齐
	* baseline 项目的第一行文字的基线对齐

- 6、align-content 属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用

	* stretch  (默认) 轴线占满整个交叉轴
	* flex-start 与交叉轴的起点对齐
	* flex-end 与交叉轴的终点对齐
	* center 与交叉轴的中点对齐
	* space-between 与交叉轴两端对齐，轴线之间的间隔平均分布
	* space-around 每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍


## [项目的6个属性](https://workflowy.com/s/Ceay.AN3QiSR6pK)

- 1. order 属性定义项目的排列顺序。数值越小，排列越靠前，默认为0
- 2. flex-grow 属性定义项目的排列顺序。数值越小，排列越靠前，默认为0
- 3. flex-shrink 属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
- 4. flex-basis 属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
- 5. flex 属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。
- 6. align-self 属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。


# Flex 布局实例

## [flex 栅格化布局实例](http://zyj1022.github.io/demos/flex-layout.html)
## [Flex 布局容器属性演示](http://zyj1022.github.io/demos/flex-properties.html)

----

>本文参考引用文档
>
> [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
>
> [Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)
