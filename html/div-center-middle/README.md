总结当前知道的对div嵌套标签水平垂直居中方法，如果还有新的方法，请不吝赐教哦。好，下面开始列举代码咯。
首先给需要实现水平垂直居中的两个div设置统一的样式
```
.styl {
	width: 100%;
	height: 300px;
	background: #ccc;
	margin: 30px auto;
	position: relative;
	border: 1px solid #000;
}
.box {
	width: 200px;
	height: 100px;
	background: #333;
}
```
## 方法一：父标签 table-cell 法
看题目就知道是利用父标签的 `display: table-cell`,该属性指让标签元素以表格单元格的形式呈现，类似于td标签。目前IE8+以及其他现代浏览器都是支持此属性的。
那么既然父标签已经是类似表格单元了，那么就可以利用表格单元的属性`vertical-align: middle;` 来使得内部元素垂直居中了，然后再给子标签 `margin:0 auto;` 即可，需要说明的是，这里的父标签需要设置宽度，不能自适应宽度

```
/*css*/
.styl1 {
	width: 1000px;
	display: table-cell;
	vertical-align: middle;
}
.styl1 .box {
	margin: 0 auto;
}
```
html代码只要用我们上面的样式即可，之后的class名只要修改 `styl1` 为对应的样式名即可。
```
/* html */
<div class="styl styl1">
	<div class="box"></div>
</div>
```
## 方法二：常规 position 及子元素偏移方法
父标签设置 `position:relative`,子标签设置 `position:absolute`,同时设置 `left:50%`,`top:50%`,这个时候子标签的左上角已经垂直水平居中，再设置`margin`的负值为子元素宽高的50%即可将中心点对齐。
此方法缺点，需要动态调整 margin 偏移量为子标签宽高度的一半这个值，无法自动匹配。
```
.styl2 .box {
	position: absolute;
	left: 50%;
	top: 50%;
	margin: -50px 0 0 -100px;
}
```

## 方法三：CSS3新属性 transform 的 translate 方法
首先 `transform` 是css3的新属性，该属性是让我们对元素进行旋转、缩放、**移动**或倾斜。那么再看属性值 `translate` 是定义2D转换，就是平面的x,y喽。知道这些，那么我们在 方法二的基础上将偏移量通过 `transform` 来设置即可。
此属性支持IE9以上属性，而且需要兼容不同浏览器写法。
```
.styl3 .box {
	position: absolute;
	left: 50%;
	top: 50%;
	transform: translate(-50%, -50%);
	-webkit-transform: translate(-50%, -50%);
	-moz-transform: translate(-50%, -50%);
	-ms-transform: translate(-50%, -50%)
}
```
## 方法四：margin:auto 方法
此方法依然是在方法二的基础上修改，将子元素的left、top、right、bottom都设置为0，然后再 `margin:auto`,即可实现子元素的水平垂直居中。
此方法好处是自适应父标签的宽高，也就是说，无论父标签宽高怎么变都水平垂直居中哦。
此方法支持IE8+以及其他浏览器。是不是很神奇，具体原因查看 **[margin:auto实现绝对定位元素的水平垂直居中](http://www.zhangxinxu.com/wordpress/2013/11/margin-auto-absolute-%E7%BB%9D%E5%AF%B9%E5%AE%9A%E4%BD%8D-%E6%B0%B4%E5%B9%B3%E5%9E%82%E7%9B%B4%E5%B1%85%E4%B8%AD/)**

```
.styl4 .box {
	position: absolute;
	margin: auto;
	left: 0;
	top: 0;
	bottom: 0;
	right: 0;
}
```

## 方法五：flex 布局方法
flex意为弹性布局，不同于盒模型布局，子元素实现垂直居中就比较容易了，具体教程可以参看 **[阮一峰的flex布局教程](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html?utm_source=tuicool)**，这里只说怎么实现。
在此我们只要将父标签设置为 `display: flex`,并进行相应的主轴 `justify-content: center` 和交叉轴上的 `align-items: center` 对齐方式就可以了。
```
.styl5 {
	justify-content: center;
	align-items: center;
	display: -webkit-flex;
	display: flex;
}
```
html结构同上
```
<div class="styl styl5">
	<div class="box"></div>
</div>
```
## 方法六：伪元素 inline-block/vertical-align 方法
这个方法主要利用父标签的 `::before` 伪元素的 `inline-block` `vertical-align`来实现，比较巧妙，但这个方法要求子元素的属性必须是 `display: inline-block`, 好处依然是父子元素都可以自适应宽高并保持水平垂直居中。css如下：
```
.styl6 {
	text-align: center;
}
.styl6:before {
	content: '';
	display: inline-block;
	height: 100%;
	vertical-align: middle;
}
.styl6 .box {
	display: inline-block;
	vertical-align: middle;
}
```
好，至此总结了div内部元素水平垂直居中的方法。
