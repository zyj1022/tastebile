# CSS之盒模型详解

盒子模型是html+css中最核心的基础知识，理解了这个重要的概念才能更好的排版，进行页面布局。

在Html文档中，每个元素都被表示为一个矩形盒子，每个盒子有四个边：**外边距边(margin), 边框边(border), 内填充边(padding) 与 内容边(content)**。 

盒模型允许我们在其它元素和周围元素边框之间的空间放置元素


## CSS盒子模型(Box Model)

下面的图片说明了盒子模型(Box Model)：

![Box Model](http://og8z552x2.bkt.clouddn.com/box-model.gif)


不同部分的说明：

* Margin(外边距) - 清除边框外的区域，外边距是透明的。
* Border(边框) - 围绕在内边距和内容外的边框。
* Padding(内边距) - 清除内容周围的区域，内边距是透明的。
* Content(内容) - 盒子的内容，显示文本和图像。

## 元素框宽高计算方法

为了在所有浏览器中的元素的宽度和高度设置正确的话，你需要知道的盒模型是如何工作的。

**元素框的总宽度** = 元素的width + padding的左边距和右边距的值 + margin的左右边距的值 + border的左右宽度；

**元素框的总高度** = 元素的height + padding的上下边距的值 + margin的上下边距的值 ＋ border的上下宽度。

## 常见的盒模型问题

[CSS中margin边界叠加问题及解决方案](../margin-margin/)

## box-sizing属性介绍

box-sizing属性是用户界面属性里的一种，之所以介绍它，是因为这个属性跟盒子模型有关，而且在css reset中有可能会用到它。

```
box-sizing : content-box|border-box|inherit;
```
- content-box 默认值，可以使设置的宽度和高度值应用到元素的内容框。盒子的width只包含内容。
	- 即总宽度 = margin + border + padding + width

- border-box 设置的width值其实是除margin外的border+padding+element的总宽度。盒子的width包含border+padding+内容

	- 即总宽度 = margin + width

- inherit 规定应从父元素继承 box-sizing 属性的值


## 浏览器的兼容性问题

一旦为页面设置了恰当的 DTD，大多数浏览器都会按照上面的图示来呈现内容。

然而 IE 5 和 6 的呈现却是不正确的。根据 W3C 的规范，元素内容占据的空间是由 width 属性设置的，而内容周围的 padding 和 border 值是另外计算的。不幸的是，IE5.X 和 6 在怪异模式中使用自己的非标准模型。这些浏览器的 width 属性不是内容的宽度，而是内容、内边距和边框的宽度的总和。

虽然有方法解决这个问题。但是目前最好的解决方案是回避这个问题。也就是，不要给元素添加具有指定宽度的内边距，而是尝试将内边距或外边距添加到元素的父元素和子元素。

IE8 及更早IE版本不支持 填充的宽度和边框的宽度属性设。

解决IE8及更早版本不兼容问题可以在HTML页面声明 <!DOCTYPE html>即可。