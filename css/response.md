# 响应式布局

响应式布局主要是指一套页面能够适应不同但终端设备，包括电脑端、移动端 (ipad、iphone)等。
在这里我们不研究如何设计响应式页面，只说如何实现，可以想到，随着设备的不同，我们的页面必然会
被这样的问题困扰，页面元素模块的宽高变化，页面中文字大小的变化。

首先说布局方面，通常先以电脑端来适配制作，完成后，就该添加媒体查询（Media Query)和针对不同设备
下的css代码了。

## 1、布局及设置 meta 标签

```
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
<meta name="HandheldFriendly" content="true">
```

`viewpor` t是网页默认的宽度和高度，上面代码的意思是，
网页宽度默认等于屏幕宽度（width=device-width），原始缩放比例（initial-scale=1）为1.0，即网页初始大小占屏幕面积的100%。

`user-scalable` 属性能够解决 ipad 切换横屏之后触摸才能回到具体尺寸的问题。

由于网页会根据屏幕宽度调整布局，

- 不能使用绝对宽度 `px` 的布局，也不能使用具有绝对宽度的元素。可以使用百分百
- 字体同样不能使用绝对大小(px)，使用相对大小 `em`、`rem`，

在设置 `rem` 时不要忘记设置根元素大小, [具体区别请看这里](./px-em-rem/README.md)

```
html{font-size:100%;}
@media (min-width:480px){body{font-size:1rem;}}
@media (min-width:768px){body{font-size:1.2rem;}}
```

布局通常采用[流动布局(fluid grid)](https://alistapart.com/article/fluidgrids)形式,页面种各个元素区块都是浮动的，如果同一行放不下两个元素，则会被‘挤’到下一行。

## 2、通过 media query 来设置样式

"自适应网页设计"的核心，就是CSS3引入的Media Query模块。

```
/* Normal desktop :992px. */
@media (min-width: 992px){ }

/* Tablet desktop :768px. */
@media only screen and (min-width: 768px) and (max-width: 991px) { }

/* Large Mobile :480px. */
@media only screen and (max-width: 767px) { }

/* small mobile :320px. */
@media only screen and (max-width: 479px) { }
```
通过以上不同设备分辨率下定义样式，基本可以做到各种终端设备自适应了。

也可以查看 bootstrap 的媒体查询

```
/* 超小屏幕（手机，小于 768px） */
/* 没有任何媒体查询相关的代码，因为这在 Bootstrap 中是默认的（还记得 Bootstrap 是移动设备优先的吗？） */

/* 小屏幕（平板，大于等于 768px） */
@media (min-width: @screen-sm-min) { ... }

/* 中等屏幕（桌面显示器，大于等于 992px） */
@media (min-width: @screen-md-min) { ... }

/* 大屏幕（大桌面显示器，大于等于 1200px） */
@media (min-width: @screen-lg-min) { ... }
```

## 3、图片的自适应

**给插入页面种图片设置自适应**

```
img {
	max-width:100%;
	height:auto;
}
```
**给标签元素中的背景图，设置自适应**

比如给logo部分设置

```
.logo {
	width:100%;
	height: 2rem;
	background-img: url(./images/logo.png);
	background-size: cover; ／*等比例扩展图片，填区域*／
}
```


> 关于布局但变化方式可以看：
>
> [自适应网页设计](http://www.ruanyifeng.com/blog/2012/05/responsive_web_design)
>
> [响应式布局设计的三大要点](https://www.douban.com/note/489518516/?type=like#sep)
>
